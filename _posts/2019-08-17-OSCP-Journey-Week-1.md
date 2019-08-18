---
layout: post
title: OSCP Journey&#58; Week 1
description: Recap of week 1 in the OSCP journey.
category: OSCP
---

### TLDR

{{ page.description }}

### Week 1 Recap

The first week of OSCP journey has passed.
It has been a really long and quite stressful week for me.
I spent around 50-60 hours studying and messing around the labs.
But I'm really glad to finally to write this recap of the week.

#### What have I done ?

The material I received is about 8-10 hours of videos along with 350-ish pages of PDF.
However I have only skimmed briefly through the material and jumped directly into the lab instead.

In the span of 5 days, I have rooted 11 boxes, including 7 Linux boxes, 1 FreeBSD box and 3 Windows boxes.
I also have limited shell for 2 boxes, including Pain - 1 of the big 4 (Pain, Sufference, Gh0st, Humble).
In addition to that I also managed to unlock IT Department subnet.

#### What haven't I done ?

I feel more comfortable when dealing with Linux environment so that's why I focused on Linux boxes at first.
This also means I haven't done much in Windows environment, which is a totally different beast.

I also haven't done the Buffer Overflow part either.
I want to leave it toward the end of the month.

#### General Thoughts

I'm quite happy with my progress so far.
I have solved most of the available Linux boxes in the Public subnet.

About the Linux boxes, I feel a little sad that there aren't a lot of privilege escalation part.
Most of the time the machine is quite old and vulnerable to (some) kernel exploit.
However generally for Linux boxes I feel quite confident with them.

#### What new techniques have I learned ?

I remember read somewhere a tip "If a box has gcc installed then probably a kernel exploit should work here".
However nobody ever told me the opposite.
During the week I faced a scenario where the box running CentOS seems really old and obviously vulnerable to a kernel exploit.
But gcc/cc/g++ is not installed locally, so I have to find a VM image of the same kernel online, compile on it then port the binary to the vulnerable machine.
It was a pain throughout.
But that's my first time doing cross-compiling.
It also makes me realize I should stock multiple VMs for different OS versions.

Other than that the whole week is mostly revising and apply techniques that I have done before.

### What now ?

I have finished most of the Linux boxes of the Public Lab, which means I finally the time for me to fully focused on Windows boxes now.
On the side note, I will have to learn about different services such as SMB, SNMP or RDP.

My plan for this week is to solve at least 10 Windows boxes in the lab, without using Metasploit.
I might need to go back to HTB for additional tips on enumerate.

That's it for this week. See you all next week.
