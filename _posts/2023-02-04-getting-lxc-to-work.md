---
layout: post
title: Getting LXC To Work
date: 2023-02-04 08:28 -0700
---

In my [last post], I tried and failed to set up LXC for deploying software.
I revisited the setup later on and got things to work, and this is what I did.
Unfortunately, I didn't start from scratch,
so this will be of limited use to anyone trying to set up LXC themselves.

[last post]: /2022/12/04/how-not-to-deploy-using-lxc.html

My goal in setting this up is to be able to do the following:
- create [unprivileged containers as a non-root user]
- create btrfs subvolumes for the containers and images by default
- allow the containers to talk to the network

[unprivileged containers as a non-root user]: https://linuxcontainers.org/lxc/getting-started/#creating-unprivileged-containers-as-a-user

I ran into several problems along the way,
but revisited LXC a few weeks later and was able to work around them.
I can now successfully create unprivileged containers with btrfs
subvolumes as a non-root user.

## Problem 1: Wrong Filesystem

`lxc-create` with `--bdev=btrfs` tries to create a btrfs subvolume
in a non-btrfs filesystem.
This is because it's trying to create it in `~/.local/share/lxc/`
instead of somewhere in `/data`.
This is also in spite of the fact that I configured the btrfs volume
as a storage pool when doing `lxd init`.
It seems that lxc (not lxd) has no concept of storage pools,
and the way to control where the image gets created is
to set `lxc.lxcpath` in `~/.config/lxc/lxc.conf` (not `default.conf`!).
Once I had done that, `lxc-create` worked as expected.

## Problem 2: No Networks For You
Although the gyrations above allowed me to create a container,
it failed to start when trying to create a virtual network interface for it.
I had uninstalled lxd in a fit of childish pique
due to the apparent disregard for the settings I had so carefully chosen
during setup with `lxd init`.
I believe that uninstallation took the `lxdbr0` network with it.
I had given my user permission to use lxdbr0 in `/etc/lxc/lxc-usernet`,
so without it, there was no way to create a virtual interface.
My guess is that installing only lxc creates lxcbr0,
but installing lxd creates lxdbr0,
and then uninstalling lxd removes lxdbr0 leaving no networks.

I couldn't find any instructions for creating lxc networks,
and there's no such commands as `lxc network` or `lxc-network`.
I eventually discovered that there is `lxc-net.service`
which was inactive and disabled.
Enabling and starting it failed to make a network show up
in the output of `networkctl list`.
The `lxc-net` service is configured in `/etc/default/lxc-net`,
which didn't exist on my machine,
so I created it and copied the configuration
from an example on [the arch wiki][arch lxc using a nat bridge].
After restarting lxc-net, `lxdbr0` showed up in `networkctl list`!
Mission accomplished!

[arch lxc using a nat bridge]: https://wiki.archlinux.org/title/LXC#Using_a_NAT_bridge

## Problem 3: You Can Never Leave
With a shiny new network, `lxc-start` finally succeeded.
The container starts in the foreground by default,
connecting you to a shell so you can run commands.
(I really don't understand why that's the default.
Docker's the same way.
Wanting to muck about inside a container 
is something I want to do occasionally---usually I just want to start it
and let it do its thing.)

There is no way to get out of the shell.

I tried `ctrl-c`, `ctrl-d`, `ctrl-z`---all the usual suspects.
Eventually I turned to the internet where a search led to someone saying
that `ctrl-a q` would work.
It did not.
Looking back, probably `kill $$` would have worked,
but what I actually did is start a new SSH session,
find the process from the host machine, and kill it.

## Success
Now that I can create, start, and stop LXC containers,
I'll be able to deploy things in them.
I was quite tired after dealing with all of the above,
so that will be an adventure for another day.
