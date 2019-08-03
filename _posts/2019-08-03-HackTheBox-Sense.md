---
layout: post
title: HackTheBox&#58; Sense
description: Writeup for HTB box Sense.
category: HackTheBox
---

### Introduction

Sense is a Linux box, difficulty Easy on HTB.

It starts by finding an unprotected credential and then a Command Injection script on EDB to get root shell directly.

### Initial Scan

![Sense-1](/resources/Sense-1.png)

### Unprotected credential

Checking web server running on port 80 we get redirected to a login page with https on port 443.
By looking at the source we can see that the service running is pfSense.
We run `gobuster` to with dirbuster medium wordlist as well as extension `html`, `php`, `txt`, we get some hits.
There is 1 special hit at `/system-users.txt`
```txt
####Support ticket###

Please create the following user


username: Rohit
password: company defaults
```
A quick Google search shows the default credential for pfSense is admin:pfsense.
With that in mind and from the ticket, we can guess that the credential should be Rohit:pfsense or rohit:pfsense.

Login with rohit:pfsense succeeds and we get to the dashboard.
![Sense-2](/resources/Sense-2.png)

### pfSense Command Injection

From the System Information dashboard, we can see that the version of pfSense running is 2.1.3.
Searching pfSense on Exploit-DB gives us a hit for RCE: [status_rrd_graph_img: EDB 43560](https://www.exploit-db.com/exploits/43560).

The script injects command by escaping the database variable with `;`. By providing the script LHOST, LPORT and the credential, we got a root shell directly and can read both user.txt and root.txt.
![Sense-3](/resources/Sense-3.png)
