---
layout: post
title: HackTheBox&#58; Valentine
description: Writeup for HTB box Valentine.
category: HackTheBox
---

### Introduction

Valentine is a Valentine box, difficulty Easy on HTB.

User part is by finding an encrypted ssh key on the server along with using SSL Heartbleed to get the passphrase.
Root part is really trivial by connecting to an existing root tmux session.

### Initial Scan

![Valentine-1](/resources/Valentine-1.png)

### User: SSL Heartbleed

The front page of the webservice running shows a picture of a girl screaming, along with the heartbleed vulnerability icon.
Coupled with the nmap result showing port 443 running, we can expect this box will be using [SSL Heartbleed bug](http://heartbleed.com).
Also [obligatory relevant xkcd](https://xkcd.com/1354/).

We can easily confirm this using nmap `ssl-heartbleed.nse` script.
![Valentine-2](/resources/Valentine-2.png)

A quick search on ExploitDB for Heartbleed shows several results for PoC code.
We use script [32764](https://www.exploit-db.com/exploits/32764) to try reading the memory.

We might need to run several time before seeing something useful but we get a hit that looks like a request to `/decode.php` with data `$text=aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==`.
We can decode the phrase to `heartbleedbelievethehype`.
![Valentine-3](/resources/Valentine-3.png)

Now we need something to use the phrase with.
By running `gobuster` with medium dirbuster wordlist, we get a hit on directory `/dev`, which contains a file named `hype_key`.

Examine the contant of `hype_key` we can see that it resembles a hexdump with all ASCII characters.
We use a quick Perl one-liner to decode it, reveal an encrypted ssh key.
```bash
cat hype_key | perl -ane 'for $b (@F) { print pack "H*", $b }'
```
![Valentine-4](/resources/Valentine-4.png)

Now we can ssh to the server as user `hype` (just like the filename), using the encrypted ssh key along with the previous passphrase.
We get a shell and we can read user.txt.
```bash
ssh -i hype_key.ascii hype@10.10.10.79
```

### Privesc: tmux

Checking all running process, we can see a root tmux process running using a socket.
User `hype` has group read/write permission to this socket, which means we can connect to it as well.
```bash
/usr/bin/tmux -S /.devs/dev_sess
```
![Valentine-5](/resources/Valentine-5.png)

tmux is basically shell organizer to save you from Alt-Tab all the time.
(Personally I use it all the time, as can be seen from screenshots)
The source code of tmux can be found [here](https://github.com/tmux/tmux).

To connect to this socket, we just need to execute the previous command again.
```bash
tmux -S /.devs/dev_sess
```
We get a root shell and we can read root.txt.
