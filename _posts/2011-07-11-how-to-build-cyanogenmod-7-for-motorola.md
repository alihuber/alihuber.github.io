---
layout: post
title: How to build CyanogenMod 7 for Motorola Milestone on Arch Linux 64
date: '2011-07-11T23:06:00.001+02:00'
tags:
- Linux
- Android
- Python
- Arch Linux
modified_time: '2011-07-11T23:07:39.410+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-4362064255762188850
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2011/07/how-to-build-cyanogenmod-7-for-motorola.html
---

First of all, you have to activate the `multilib`-repo in `/etc/pacman.conf` and install some 32-bit libraries. To be honest, I have forgotten which one exactly because I have a lot more installed than I need for building Android (I also have programs like AdobeAIR running). However, a good starting point of figuring out which packages are needed [are](http://mysite.verizon.net/ian.pilcher/f13-android-howto.pdf) [these](http://fedoraproject.org/wiki/HOWTO_Setup_Android_Development) [links](http://fedoraproject.org/wiki/Google_Android). These pages/documents have all the other instructions in it, like how to install the AndroidSDK without pacman. Just follow all the instructions, they work fine. The packages for Arch are named the same, except for the `-devel` suffix, and the 32-bit libraries begin with `lib32-`. And you need to install `jdk`, of course. If there are any libs or files missing, they will be pointed out during the build process, so you can install them later. One package you will need for sure is `gcc-multilib` instead of the normal `gcc`. You can install it with the metapackage `multilib-devel`. Remove all the conflicting packages. Another package not on the lists which you will need is `schedtool`.  

The next step is to get the "`repo`"-script running. Since Arch switched to Python3 as default it won't work out of the box. For some guys it worked to just alter the `python` symlink to point to `python2` instead of `python3`, or just rename every occurence of `python` to `python2` in the repo script, or just alter the shebang-line. But none of these ways worked for me.  
I found a solution in [this forum thread](https://bbs.archlinux.org/viewtopic.php?id=109288), creating a user with a local Python 2.7 installation did the trick. I created a new Unix-user and named it something like "py2" and followed the steps in the last post in the thread. All the next steps have to be made with this account.  

When you have installed all the needed packages and start the build you will run into the next issue, regarding Perl. You will get an error like `"Can't locate Switch.pm in @INC"` (despite the fact that the file `Switch.pm` is existent in my file system, but I'm no Perl programmer and therefore have no clue), the line `use Switch;` in the `make_names.pl`-file is the one which causes the error. It seems that since a few versions ago, the `switch` statement was removed from the Perl language and has to be installed seperately. Unfortunately, this build script relies on Perl with a switch statement.  
Luckily, there is a package for perl-swith in the [AUR](http://aur.archlinux.org/packages.php?ID=34534). The first time I encountered this I had to patch the sources manually with the diff-file mentioned in the comment on the AUR page, but it is updated by now and you can just install perl-switch from the AUR.  
After you placed the `repo`-script in the `/home/username/bin`-folder like on the Fedora-wiki-page you can finally follow the instructions on [http://android.doshaska.net](http://android.doshaska.net).  
When you do your first pull with git it will ask you to give some contact information. Run the `git config` commands as instructed. All the `repo`-commands should run normally and download all the Android sources. With a lousy connection like mine this can last for days, because it will download something about 10 gigabytes.