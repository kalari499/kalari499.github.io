---
layout: post
title: HackTheBox&#58; Nibbles
description: Writeup for HTB box Nibbles.
category: HackTheBox
---

### Introduction

Nibbles is a Linux box, difficulty Easy on HTB.

The initial foothold for the box is weak admin credential for the blog, then we use an Arbitrary File Upload to get our shell.
Privesc part is trivial using sudo.

### Initial Scan

![Nibbles-1](/resources/Nibbles-1.png)

### Initial Foothold: Nibbleblog

Checking the webservice running at port 80, we only see a Hello World message.
By checking the source code we can see a comment about `/nibbleblog`, and that leads us to the blog URI.

Next step for us is using `gobuster` with dirbuster medium wordlist (base URL is `/nibbleblog`) to find some common folders.
We get some hits like `content`, `themes`, `admin`, `plugins`, `languages`,...
If we look at `/nibbleblog/content/private/config.xml` we can see that there should be an user named admin (admin@nibbles.com)

A login page is presented at `admin` or `admin.php`, so we will try some weak and guessable credentials such as admin:password, admin:admin, admin:nibbles.
A small note here is that if you fail too many times (5 times), you would get blacklisted temporary, which means we can't brute force it but we have to guess easy credentials instead.
Fortunately for us (not really), admin:nibbles is the correct credential and we get ourselves access to the admin page

### User: my\_image - Arbitrary File Upload

Looking at general setting in the admin panel, we can see that the blog is running Nibbleblog 4.0.3 "Coffee" version.
A quick search for Nibbleblog on Exploit-DB gives us 1 hit on 4.0.3 version for Arbitrary File Upload.

We can also read the original report for the vulnerability [here (curesec.com)](https://curesec.com/blog/article/blog/NibbleBlog-403-Code-Execution-47.html).
The vulnerability is at my\_image plugin, where it never checks filetype or extension of file uploaded (only warning).
![Nibbles-2](/resources/Nibbles-2.png)

The file uploaded is saved in a predictable location at `/nibbleblog/content/private/plugins/my_image/image.php`.
A simple php reverse shell gets us a shell running as user `nibbler` and we can read user.txt.
![Nibbles-3](/resources/Nibbles-3.png)

### Privesc: sudo

Running `sudo -l` shows that user Nibbler can run an executable file at path `/home/nibbler/personal/stuff/monitor.sh` (never do this in real life please).
From here privesc is trivial, we just need to create folders `/home/nibbler/personal/stuff` and execute `bash` inside `monitor.sh` file.

We get ourselves a root shell and we can read root.txt.
![Nibbles-4](/resources/Nibbles-4.png)
