---
title: How Not To Deploy Using LXC
layout: post
date: 2022-12-04
---

I seem to have a pathological need to do things the hard way.
This is a braindump of things I've learned
and things I'm still confused about for working with LXC.

I would like to host several services on a single server,
and I decided to use [linux containers] (LXC)
instead of something more mainstream like docker.
This decision may have been inspired by [NixNet's decision to use LXC][nixnet].
To make things even more interesting, I wanted to use a [Hetzner Volume],
and encrypt it with LUKS following
[Will Webberly's guide to volume encryption][wilw-encryption].

[linux containers]: https://linuxcontainers.org/
[nixnet]: https://docs.nixnet.services/Infrastructure#Isolation_.26_flexibility
[Hetzner Volume]: https://docs.hetzner.com/cloud/volumes/overview/#volumes
[wilw-encryption]: https://wilw.dev/notes/volume-encryption/

First off, there are several things with similar names:
- LXC is the overall container ecosystem.
- `lxc` is a command used to manipulate LXC containers, images, etc.
- LXD is a daemon that wraps LXC in a powerful and convenient REST API
- `lxd` is a command used to work with LXD

I am still a bit fuzzy on exactly which command should be used
in any given situation.
As a result I may be trying to mix `lxc` and `lxd` in strange ways
which probably doesn't help.

## Volume encryption
This process went just fine.
I was a bit skeptical about whether it would be possible
to apply encryption encryption on top of a Hetzner volume
since it's probably some kind of networked storage
rather than a regular drive.
I just followed [Will Webberly's tutorial][wilw-encryption]
and everything worked just fine.

I did get a bit confused about the mapped device name.
After running `cryptsetup` on `/dev/sdX`,
the named encrypted volume showed up in `lsblk` under `/dev/sdX/<name>`.
That name doesn't work for formatting and mounting though,
you have to use `/dev/mapper/<name>` instead.

## Storage Pool Configuration
Running `lxd init` as root allows [interactive setup][lxd-interactive]
of various options, including a default storage pool.
I went through this carefully, mostly selecting default options
except for configuring the encrypted volume as a default storage pool.

[lxd-interactive]: https://linuxcontainers.org/lxd/getting-started-cli/#interactive-setup-options

At the end, there is an option to output the YAML preseed configuration.
I expected this command to also apply the configuration,
but it doesn't seem to.
Later, when trying to create a container,
lxc seemed to have no idea what default storage pool I was talking about,
and tried to create a btrfs subvolume on a non-btrfs filesystem,
which went about as well as you'd expect.
I thought this was due to the configuration not being applied,
but after applying it by piping it into `lxd init --preseed`,
the problems with the storage pool continued.

By the way you can't give `lxd init --preseed` a file,
the yaml has to be on stdin.

## Reformatting Storage Pool Volume as BTRFS
I had originally formatted the storage volume as `ext4`,
but using BTRFS for the LXD storage pool has several advantages.
It makes snapshots and backups fast and easy,
and each container will be assigned its own btrfs subvolume
which I think will facilitate enforcement of storage quotas.

When creating a subvolume through LXD, it runs `mkfs.btrfs` for you.
This failed because the volume already contained an ext4 filesystem.
You can pass `--force` to `mkfs.btrfs`
to tell it to overwrite the existing filesystem anyway.
I did this outside of LXD to reformat the volume as btrfs,
but LXD still wants to do its own reformatting,
and I don't think passing `--force` is possible from outside LXD.

Therefore I used `wipefs` to remove the existing filesystem
before re-running the LXD storage volume creation.
Except that didn't work because `wipefs` seems to not remove btrfs filesystems
by default.
I had to use `wipefs --all`.
Probably `wipefs --all --types btrfs` would have been safer.

## Networking Configuration
I configured the network during `lxd init` and `lxd init --preseed`,
but I'm not convinced it was actually setup
because running `lxc profile show default`
doesn't mention anything about subnets.

Although `lxc network list` includes `lxdbr0`,
and `lxc network show lxdbr0` lists subnets, so maybe it is configured.

## Permission Confusion
There is apparently a way to [create unprivileged containers
as a (non-root) user][lxc-unprivileged-user],
but pretty much whenever I run `lxc` as non-root,
I get permission denied on `/var/lib/lxd/unix.socket`.

[lxc-unprivileged-user]: https://linuxcontainers.org/lxc/getting-started/#creating-unprivileged-containers-as-a-user

That's where the LXD daemon listens.
You can add a user to the `lxd` group to grant them access to that socket,
but doing so is equivalent to making that user root
according to [the Security section of the LXD readme][lxd-security].
The whole point of creating containers as a non-root user
is that they're not root!

[lxd-security]: https://github.com/lxc/lxd#security

I have tried the following all as a non-root user:
- `lxc-create --name=alpine-test --bdev=btrfs --template=download --
  --dist=alpine --release=3.17`
- `lxc-create --name=alpine-test --bdev=btrfs --template=download`
- `systemd-run --unit=alpine-test --user --scope --property="Delegate=yes" --
  lxc-create --template=download --bdev=btrfs --name=alpine-test`
- lxc launch alpine/3.17 alpine-test --storage default

They all either try to create a btrfs subvolume in the wrong place
or try to connect to the LXD socket and are denied.
