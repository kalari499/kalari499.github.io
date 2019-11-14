---
layout: post
title: Tips&#58; Local port forwarding through SSH
description: How to do local port forwarding with SSH to access the internal network.
category: Tips
---

### Context

On the making of [Project Incredibles](/Incredibles), I encountered a fun problem.
I have a webserver (in a VM) located inside the internal network, which is hidden behind the firewall.

I want to be able to edit web content of the server (through SSH), and also see the result of my web page on Firefox.

Sounds simple, but the catch is I want to work from my main laptop, and because of the firewall, no direct connection can be made to the webserver.
So the solution for this is to do local port forwarding with SSH through the host hypervisor.

![Screenshot](/assets/05-1.png)

As a note, port forwarding is also one of the common techniques used by attackers when dealing with large companies.
The idea is that sensitive and interesting stuffs are stored in the internal network, which is obviously not accessible from the outside.
But the attacker can compromise a public-facing server, then use it as leverage for lateral movement inside the network.

### Show me the Terminal Magic

All I have to do to make it works is to run
```bash
ssh -L 7000:<Webserver IP>:80 kalari499@<Hypervisor IP>
```

Here \<Webserver IP\> is the IP address of the webserver (behind firewall), which is *dash* and \<Hypervisor IP\> is the address of the hypervisor (inside local network).
Of course, kalari499 is my account on the Hypervisor IP.

Alternatively, after normally ssh normally, in a newline prompt, type `~C` to open SSH port forward prompt (you should see no output while typing this)
```bash
-L 7000:<Webserver IP>:80
```

![Screenshot](/assets/05-2.png)

### Explanation

So the idea behind port forwarding is very simple.
You have 3 machines: A, B, C, and you are at A.
You know that A-B and B-C can connect but not A-C.
So you ask B to be the middleman for the connection.

More precisely, every time you make a connection to lhost:lport of A, your ssh connection will forward it to B, then B will make a connection to rhost:rport of C.
From the perspective of C, all it sees is connection from B and it can not see that A is making the connection.
Of course the response from C to B will also be forwarded back to A.

Here A is my laptop, B is the hypervisor and C is the webserver.
I want to see my webpage, which is located on port 80.
This means rhost is address of C, and rport is 80.
Also lhost can be localhost - 127.0.0.1, and lport can be anything (here I choose 7000).

The syntax for the command is pretty simple, add a flag `-L 7000:<Webserver IP>:80` because the lhost can be omitted.
