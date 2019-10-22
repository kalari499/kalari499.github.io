---
layout: post
title: I created my own Linux distro with Linux from Scratch
description: Today I finish my own Linux distribution, entirely from sources, with the help of Linux from Scratch project. Here are my thoughts.
category: Programming
---

### Foreword

I have been distro-hoping a lot for the last few months.
I think I tried something like 20-ish different Linux and \*BSD distributions.
Each has its own niche, own advantages and disadvantages.

Some distros are more oriented toward to average users, like Ubuntu or Mint.
For example, they come with a hastle-free installation process and several default packages and settings.
You just need to click install and the distro will install itself for you.

On the other hand, some distros offer users more control over the system, like Arch or Gentoo.
It gives you a bare minimum working system then you can do whatever you want to customize it and install whatever packages you want.
This means you have to install your own desktop, sound support or bluetooth support.

I don't know about you, but I really like the idea of building my distro from the bare minimum and having total control over my computer, what it does and what I can do with it.
It is fascinating to see how a functional system comes together, personalized to me and my needs.

So how exactly does "the bare mininum" created then ?
That's the question that Linux from Scratch project can answer.

### What is Linux from Scratch project ?

There is a common metaphor in the Linux community that goes like this:
If Ubuntu is a cake you bought from store then Arch is making your cake with flour and eggs.
And Linux from Scratch is like grind your own wheat and raising your own chickens.

Just like the name suggest, it is aimed to guide you how to build a system from scratch.
You are given the source codes of some essential packages such as bash or perl, even the Linux kernel itself.
You are also given the instructions about how to compile them and combining everything together.
The result is an extremely basic system, like really basic, much less than you could imagine.

The general installation process goes like this
- Partition for some free spaces
- Create some temporary tools using host system
- Use the temporary tools to create new tools that are dependent from host system
- Use the new tools to bootstrap everything else
- Configure everything
- Make the system bootable

### What have I learnt throughout the process ?

First is obviously how packages and programs are compiled and installed.
It was really insightful (and painful) trying to understand general methodology of configure and Makefile (automake).
Also on the side was dealing with dependencies, which packages should be installed before which.

In traditional Linux distros, this would be done fully by package managers such as apt-get or pacman under the hood.
I always take package managers for granted, not realizing how crucial and impactful they are.

More important I could see and feel how Linux comes together to be a functional system, how every packages depend and interact with each others.
I was explained in details about pretty much every low-level topic about Linux.
- Linux filesystem and its standard hierachy in general, stuff like /dev, /proc or /sys which I never had to deal with in the past.
- Partitioning and mounting disks and devices, writing /etc/fstab.
- Managing init system and services
- Booting with GRUB, writing grub.cfg
- ...

### What were the problems I faced ?

First of all, it was really time-consuming to compile code, especially on weak computer like mine.
For example, compiling gcc took me around 2 hours to finish, and I had to compile it like 5-6 times in total.

Everything required a lot of attention to details.
A lot of people are familiar to the classic ./configure && make && make install right ?
Except here configure needed 5 different flags to disable not-there-yet libraries,
then the second time it needed another 5 different flags to enable things.
Binaries and libraries needed to be installed in correct place and symlinks were everywhere.

As a result of course, they were very prone to errors, which could lead to detrimental result.

At one point I couldn't installed XML parser, which leaded back to error in Perl installation.
It took 30 minutes every time I changed config and re-compiled Perl.
Looking up error message online only showed a therads from years ago in LFS 6.0 (current version is 9.0).
Of course it had no answers.

![Relevant XKCD](/resources/01-xkcd.png)

Took me around 2-3 days to debug it.
The problem was a missing flag while installing bzip2 (from 20 packages ago)
So bzip2 library was installed in wrong location, and everything was using wrong library path.

This is just 1 example of what I faced through the process.
In general just a lot of small things lead to big effect and debugging them is hard.

### Any afterthoughts ?

- Very frustrating to build and debug but very joyful to see the distro boot and work
- Final Linux distro is around 16 MB and can still be much smaller -> Can be used for embedding devices
- Package managers are amazing
- Not realistic for everyday usage because following development updates and security updates is harder without a packet managers
- For future revising: Add a package manager, a X.org server and a desktop environment
- You should do it if you want to look under the hood to learn about the system you are using (Linux)
