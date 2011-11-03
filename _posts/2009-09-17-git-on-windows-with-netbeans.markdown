--- 
layout: post
title: GIT on Windows with Netbeans
wordpress_id: 84
wordpress_url: http://blog.elinkmedia.net.au/?p=84
date: 2009-09-17 16:58:21 +10:00
---
I've been using SVN (via Tortoise SVN) on my windows development machine for a while. The experience is quite average. It does what I need, but I still had to play with my local Apache configuration and the speed is not that impressive. GIT is apparently becoming the industry version control program these days, so I decided to give it a try!

To set it up on Windows, it's actually quite simple. You first need to download "msysgit", available from Google Code. Follow the instruction to install it. Since we are using Windows, to play safe, I chose the installation location to be at "C://GIT", so that we will not potentially run into the infamous "C://Programs Files" issue.

So ... after installation, we are pretty much good to go. Fire up the newly installed Git Bash, you then pretty much work in a "linux-like" shell, with only git of course :)

I'm using Netbeans for my development and am naturally lazy, so that I thought it'll be better if I can do my version control stuff inside the IDE. I got lucky again. Download "NBGit" from Google Code (wonder why the downloads are not from GitHub ...), and load it up with Netbeans. All good.

I then ask, how do I push the source code to a remote location/computer, so that if my local computer crashes, I can still have my version controlled copies of everything?

If you don't mind others to see and grab your code, go to GitHub. If you don't mind spending some dollars every month to be able to host private projects and you want to access the files from everywhere , go to GitHub. Otherwise read on ...

If you have a spare machine sitting at home and not doing much, you then might want to load it up with your favorite Linux OS. Install git-core, and SSH server. All you need to do now is to setup a few empty GIT repositories on the Linux machine, note down the Linux machine's IP and your empty GIT repository locations. You have a remote GIT server. I'm not going to describe how to do each of these tasks in details, since there are a lot good tutorials on the net already.

Anyway, that's the setup I use, and I hope you may find it's useful.
