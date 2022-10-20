---
title: "Login issues with newer browsers"
author: garrett
tags: cockpit
---

## Quick summary

Firstly, there's a fix.

In most cases, update. [Cockpit 277](cockpit-277), released last month, has the fix.

Updating Cockpit should work for Arch, CentOS Stream, Debian, Fedora, openSUSE, non-LTS versions of Ubuntu.

If updating doesn't work (in Ubuntu LTS, some versions of RHEL, Alma Linux, and Rocky Linux), then hopefully it will work very soon. For the time being, please [look at the bottom of this post for some workarounds](#workarounds).

## Summary

Logging in with new versions of browsers into a few older versions of Cockpit is currently broken due to a check in the login code. This check used to pass in all browsers, but very recently every browser has changed the way they parse the string. Firefox release 106 — released this week — has this parsing change; all other browsers will have it in upcoming versions too.

We fixed this bug as soon as it was discovered (a month ago) and pushed [a new release of Cockpit](cockpit-277) and also backported it as a hotfix to all the "stable" slower-moving distributions.

All distributions have had the fix for a around a month. Most distributions have accepted and published the fix, including Arch Linux, CentOS Stream 8 & 9, Debian, Fedora, openSUSE Tumbleweed, Red Hat Linux Enterprise Linux versions prior to 8.8, and Ubuntu Kenetic.

Due to the nature of enterprise/LTS distributions the fixes have not landed yet in Red Hat Enterprise Linux 8.6 & 9.0, Ubuntu 22.04 LTS, Alma Linux, and Rocky Linux.

## Affected browsers

Right now (2022-10-20): Using Firefox 106 with Cockpit is affected. However, Chrome, Edge, Brave, and any other Chromium-based browser will have versions in the very near future which will also be affected. WebKit-based browsers, such as Safari and GNOME Web will likely be affected soon as well.

Firefox ESR (Extended Support Release) is slower-moving and is on 102.x and is not affected, nor will it be affected until Q3 2023 (2023-07-04 specifically, according to [the Firefox release schedule](https://wiki.mozilla.org/Release_Management/Calendar)). 

## Workarounds

### Ubuntu

#### Official packages in "proposed"

For Ubuntu, the fix should be out in the main repo next week.

In the meantime, the fixed version of Cockpit has been accepted to the proposed repository (a staging repo). You can enable this repository or even just cherry-pick specific packages (such as Cockpit packages, like `cockpit-ws`). More information is available at [Ubuntu's EnableProposed wiki page](https://wiki.ubuntu.com/Testing/EnableProposed) and [at Launchpad](https://bugs.launchpad.net/ubuntu/+source/cockpit/+bug/1990623/comments/10). If you test this and it works for you (it should!) please also leave a comment [at the Launchpad issue](https://bugs.launchpad.net/ubuntu/+source/cockpit/+bug/1990623) to let them know to push it to the main repos sooner, so everyone will get the fix!

#### Unofficial testing PPA

If you can't use the proposed packages for whatever reason, then [Martin Pitt, an official member of the Cockpit team and Ubuntu and Debian package maintainer, has his own testing PPA](https://launchpad.net/~pitti/+archive/ubuntu/fixes/). This is almost as official as it gets, coming from Martin. It's just not an "official" package by the distribution.

### Rocky Linux 9 and Alma Linux 9

CentOS Stream has fixes. The packages are likely binary compatible, but you can rebuild the source packages. It's unknown at this time if Alma and Rocky are building the new version or not.

### If you cannot install non-official packages or packages are not yet available

If you can't use either packages (for whatever reson) or packages for your distribution are not yet available, then [I wrote a simple 1-line sed command that you can use as well](https://github.com/cockpit-project/cockpit/discussions/17800#discussioncomment-3840603). It hotpatches your existing system by adding two (necessary) characters to a JavaScript file.

We suggest using an official or semi-official package instead, if you can. But we're including this quick fix for those who do not have packages available or who cannot update package versions (for whatever reason).

This is a simple `sed` command that adds the two characters to the webpacked JS file for the login page. Run it as an administrator (`root` account) on a server with Cockpit installed:

```
sed -i 's/is():where()/is(*):where(*)/' /usr/share/cockpit/static/login.js
```

It works on every distribution that has Cockpit's `login.js` in `/usr/share/cockpit/static/`. If your distribution has the file elsewhere, then change the path accordingly.