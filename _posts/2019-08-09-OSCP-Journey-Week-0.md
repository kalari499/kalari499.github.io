---
layout: post
title: OSCP Journey&#58; Week 0
description: Recap of week 0 in the OSCP journey.
category: OSCP
---

### TLDR

{{ page.description }}

### Week 0 Recap

There is a list of HTB boxes that are similar to OSCP, created by [TJNull](https://twitter.com/tj_null)
It has been shared around quite a lot, including HTB Discord and OSCP Discord so I thought I would give it a try.

![OSCP-1](/resources/OSCP-1.jpg)

In addition to just rooting them, I also tried to document the way I did it in a decent standard.
You can find a lot of writeups for them in this blog.
In the list there are some I had already solved before, but I still did them again anyway for documentation.

#### What have I done ?

I managed to finish almost all the boxes in the Linux column, except Kotarak and Node (I haven't touched them).
Most of the boxes were quite easy, took me about 2-3 hours max to solve.
Moreover I also managed to publish 10 writeups for them.
However there were some like Brainfuck (Insane) or Tartarsauce (Medium) that caused me a lot of troubles and slowing me down, took me 1-2 days.

#### What haven't I done ?

This week I haven't touched any of the Windows boxes at all.
Although I do think that focusing all week on Linux then Windows for next week is better for learning about certain serivces/techniques than mixing environment.

#### What new techniques have I learned ?

A thing I notice in the Linux boxes section is that most of them has a CVE / public exploit.
This week is the first time I encountered Wordpress in a box and learn how to enumerate with `wpscan` as well as exploit through vulnerable plugins/themes.

Another thing I noticed is that a lot of boxes has vulnerable credentials that can be bruteforced.
Normally in HTB, people don't like bruteforcing because it's not fun.
Also `hydra` syntax is really long and complicated so I never have to use it until now. 

As an unexpected bonus, I also learnt a lot about RSA encryption because root part of Brainfuck required cracking a message that has been encrypted by RSA.
I spent 1 day revising all the math and crypto part.

### What now ?

My PWK/OSCP course starts today.
I would receive the course materials soon including a very long PDF and a lot of videos.
My plan for this week is to skim through the material for a couple of days.
I will also try to tackle the first network range in the lab.

That's it for this week. See you all next week.
