---
layout: post
title: HackTheBox&#58; Poison
description: Writeup for HTB box Poison.
category: HackTheBox
---

### Introduction

Poison is a FreeBSD box, difficulty Medium on HTB.

User part of the box is quite easy, using a Local File Inclusion vulnerability to find the password backup file for ssh.
Prvesc part of the box is less common through a local running Xvnc service of user root.

### Initial Scan

![Poison-1](/resources/Poison-1.png)

### User: Local File Inclusion

Checking the webserver running on port 80, we can see a page for testing script such as `ini.php`, `phpinfo.php`.
Running the script named `listfiles.php`, we can see list of files in current folder.
One of them sticks out like a sore thump is `pwdbackup.txt`.
![Poison-2](/resources/Poison-2.png)

By modifying the parameter `file` of `browse.php` script, we can see other file than just the php scripts.
This is called [LFI vulnerability](https://en.wikipedia.org/wiki/File_inclusion_vulnerability).
Changing it to `pwdbackup.txt` we can see a message with a huge base64 string.
```text
This password is secure, it's encoded atleast 13 times.. what could go wrong really..
<base64 string>
```

A quick Perl script to decode the password reveals the password is `Charix!2#4%6&8(0`
![Poison-3](/resources/Poison-3.png)

Using LFI again, changing the parameter to `/etc/passwd` we can see there is 1 user in this box: charix.
Login through ssh with `charix:Charix!2#4%6&8(0` gives us the user shell and we can read user.txt.

### Privesc: TightVNC

There is a zip file `secret.zip` (password protected) in the HOME directory of charix.
Using scp to download the file locally and extract it with password `Charix!2#4%6&8(0`gives us a file named secret with some unreadable characters.

Checking all the process, we see a non-common process running as root.
```text
Xvnc :1 -desktop X -httpd /usr/local/share/tightvnc/classes -auth /root/.Xauthority -geometry 1280x800 -depth 24 -rfbwait 120000 -rfbauth /root/.vnc
```

This is a VNC server running locally.
Looking into `/tmp/.X11-unix` we can see `X1` socket, as well as `X1-lock` in `/tmp`.
This implies the session has display number 1 and uses TCP port 5901.
We can confirm this by running `sockstat -l`.

We will be using a program named `vncviewer` on our Kali machine to connect to the server.
Since port 5901 on the box only listening on localhost (127.0.0.1), so we need to do SSH tunneling to connect to the port.
Read more about SSH tunneling [here](https://www.ssh.com/ssh/tunneling/example).
```text
ssh -f -N 5901:127.0.0.1:5901 charix@10.10.10.84
```

The VNC session is password protected, and the last step is to find the password for it.
Here the password file is `secret` file that we unzip at the beginning, and supply the file with `-passwd` flag.
```text
vncviewer 127.0.0.1:5901 -passwd secret
```
![Posion-4](/resources/Poison-4.png)
We get a root shell and we can read root.txt.
