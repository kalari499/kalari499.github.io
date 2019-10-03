---
layout: post
title: Kali Linux vs ParrotOS, which one ?
description: My personal view on the comparisons between Kali Linux and Parrot OS&#58; 2 popular OS 
category: Linux
---

## Foreword

A couple days ago, somewhere around Sept 25 afternoon I think, ParrotOS finally surpasses Kali Linux on the popularity ranking of [Distrowatch](https://distrowatch.com/).
It's pretty cool to see Parrot is gaining more traction in the cybersecurity field.

I myself have experience using both OS (and their tools) for quite some time now,
so why not making a quick introduction about them as well as personal comparison between the 2.

## About Kali Linux

Most people here probably have heard about Kali Linux, whether you are a security expert or a script kiddie.
It's a distro designed for security, forensics and penetration testing, or as people said, a distro for "hacking".
It's created and maintained by Offensive Security.

Kali Linux is based on Debian Testing, which is Debian 10 Buster at the moment.
Standard Kali Linux is about 2.9GB with GNOME as the default desktop environment, but it also supports LXDE, MATE, KDE, XFCE.

You know the movie Mr.Robot ?
It's even featured in the movie as the distro Elliot uses when he needs to hack something.
If you don't know the movie, you really should go watch it.
It's an amazing and realistic portrait of cybersecurity and hacking.

A quick fun fact, in the movie, Elliot opts for Linux Mint as daily driver instead of other "geeky" distros.
![Kali in Mr Robot](/resources/01-1.jpg)

## About ParrotOS

ParrotOS is created roughly at the same time with Kali Linux (2013), and with the same purpose,
but for some reasons it's quite less popular, to the public at least.
It's a distro for security, forensics and penetration testing.
In addition to that, it's also bundled with stuffs for normal daily usage as well,
such as anonymous surfing tools (e.g Tor Browser) or Office Tools, even Bitcoin Wallet.

ParrotOS is based on Debian Testing (Buster).
Standard ParrotOS Home version is about 1.9 GB while Security version is about 4GB.
Default desktop environment is MATE, and the other DE that Parrot supports is KDE.

![Parrot](/resources/01-2.jpg)

## Comparison

First, about aesthetic, I perfer definitely perfer ParrotOS design more.
The set of wallpapers and general color scheme of ParrotOS are more colorful (just like a parrot).
ParrotOS comes with MATE by default, which is quite modern and lightweight.
Personally I perfer XFCE but MATE is also cool.
![Parrot](/resources/01-3.jpg)

Kali uses GNOME by default, which is quite heavy-resource and is known for causing occasion lags.
My computer is quite potato, so I personally have experience that everytime I forgot to turn off Firefox after 3-4 hours.
I always have to shutdown with the classic "Hold the power button until it's off".
Not sure why this thing is still working.
I have grown quite a lot of hate for GNOME desktop because of its "20-second boot" from both Ubuntu and Kali.

But more importantly, aside from aesthetic, you need to consider their usage in security stuff, which is literally they were designed for.

They are both rolling release, based on Debian Testing, which is understandable because you want the latest tools for the job.
Most of their tools are common, or it would be super easy to install a common tool with just `apt-get` or `git clone`.
They are loaded with a lot of tools for forensics and pentesting.

However there is a major different between them.
Kali Linux by default running as root always, which means
everything you run would be root, from Xorg server, lockscreen to Wireshark,...
Because of this, it is official stated in the main webiste to be "not suited for daily OS"..

ParrotOS on the other hand, is designed to be a daily driver.
Both Parrot Home and Parrot Security have seperated root and daily user with password for sudo (standard design).
They are also quite privacy-oriented, bundled with built-in private chat, AnonSurf by sending everything through Tor (which is REALLY cool, just like Tails) and
several Firefox add-on like NoScript or Privacy Badger.
I don't have to boot up Tails anymore for it.

## How about BlackArch ?

I haven't tried using BlackArch, mostly because I couldn't burn freaking 16GB image to my 8GB USB.
I really enjoy its color scheme tho.
It gives a vibe of anarchism and hacktivism.

## The End

ParrotOS is a clear winner to me over Kali Linux, in term of design and usage.
I'm actually considering switch from Arch to Parrot Home completely.
I think everybody should give ParrotOS a try, even Parrot Home for folks who are not in security.

That's it for today, see you next time.
