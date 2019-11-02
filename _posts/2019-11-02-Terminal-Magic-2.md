---
layout: post
title: Terminal Magic&#58; Port forwarding through SSH
description: How to do basic port forwarding with SSH.
category: Tips
---

### Context

On the making of [Project Incredibles](/incredibles), I encounter a fun problem.
I have a webserver (Dash) located inside the internal network, which is hidden behind NAT.

I want to be able to edit web content of the server (through SSH), and also see the result through HTTP{,s} on Firefox.

However I want to work from my main laptop, and there are no direct connections to the webserver.
So the solution for this is to do local port forwarding with SSH through the host hypervisor (MrIncredible).

![Screenshot](/assets/05-1.png)

### Avada Kedavra

```bash
ssh -L 7000:dash:80 -L 7001:dash:443 kalari499@MrIncredible
```

Here MrIncredible is defined inside */etc/hosts* to be the IP of remote SSH server (MrIncredible), and dash is the address of remote webserver (Dash)

Alternatively, after normally ssh normally, in a newline prompt, type `~C` to open SSH port forward prompt (you should see no output while typing this)
```bash
-L 7000:dash:80
-L 7001:dash:443
```

![Screenshot](/assets/05-2.png)

### Explanation

- *kalari499@MrIncredible*: SSH to user *kalari499* on machine *MrIncredible*
- *-L 7000:dash:80*:
    + The syntax for local port forwarding is `-L local-host:local-port:remote-host:remote-port`.
    + If *local-host* is not specified then it is interpreted as localhost (127.0.0.1).
    + SSH would open a listener on *local-host:local-port* and act as a transporter (forwarder) to *remote-host:remote-port* on the behalf as user you are SSH in.

As can be seen from the picture above I just need to go to *localhost:7000* on Firefox to access to the content on *dash:80*.
From the perspective of *dash* server, all it sees is HTTP request of user *kalari499* of *MrIncredible* machine.
From *kalari499@MrIncredible*, I can continue ssh to Dash machine to edit content of normally without interupt the port forwarding process.

### Notes
- Because I'm using a VPN at the moment, which uses a private DNS server, so I have to manually set the correct IP for MrIncredible machine in */etc/hosts*. If I set the machine to use Elastigirl machine to be the DNS server, it would forward DNS requests through school DNS server, which is not desirable.
- A small thing I notice is in local port fowarding, the remote address is resolved by the remote SSH server instead of local machine. Here I use *dash* as the remote address, which my local machine doesn't understand but only the remote SSH server understands by looking up on Elastigirl.
