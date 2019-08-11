---
layout:     post
title:      "A Super-simple AUR update script"
date:       2019-08-10
comments:   true
---

Keeping packages up-to-date is an important part of system maintenance.
The package manager handles all the official packages,
but I use lots of packages from [the AUR](https://aur.archlinux.org/)
which doesn't come with a package manager.
Since anyone can publish a package on the AUR,
it is important to make sure there is no malicious code
in the package or installation scripts.
This must be done manually every time a package is updated, so it gets tedious.

Here is my solution to alleviating the monotony.
It is a super simple script.
All it does is update a single package.
It doesn't even resolve dependencies.
You still have to review the changes and approve the update,
but it automates all the other steps.

AUR packages are accessed and distributed via a git repo,
so the first step is to fetch the latest changes
{% highlight bash %}
git fetch
{% endhighlight %}
Next, the user needs to look at and approve the changes.
We can show them with
{% highlight bash %}
git show HEAD..origin/master
{% endhighlight %}
and then ask for approval with
{% highlight bash %}
read -p "Continue? [y/n] " choice
case "$choice" in
    Y|y ) update;;
    N|n ) echo "Please delete this package and disable notifications" && exit 0;;
    * ) echo "Invalid" && exit 1;;
esac
{% endhighlight %}

`update` is just a function which merges in the changes,
builds the updated package, and installs it (with the `-i` flag):
{% highlight bash %}
function update() {
    git merge --ff-only origin/master && makepkg -i
}
{% endhighlight %}

I'd love to hear any suggestions you may have to improve this script.
Let me know what you think in the comments, via email, or on twitter.
