---
layout: post
title: Let's Build a Company (part 1)&#58; Planning
description: Initial planning of Doofenshmirtz Evil Incorporated
category: Random
---

### > Story Time

Recently a close friend of mine went for an exchange semester abroad.
He had too many things to bring with him, so he had no choice but to throw some stuffs away.
So that's how I got a new spare laptop with me.
The laptop itself is pretty okay: Intel i3-4005U, 1.7 GHz with 4GB RAM and 1TB hard drive.
Unfortunately I couldn't somehow combine computer power to make League of Legends run smoother.

What would you do when you have a spare computer ?
For me, I spent the whole week installing all kinds of Linux distro on it.
At one point I had Windows 10 and 4 different distro dual boot (or quint boot ?) at the same time.
It was really fun installing stuffs but then it would just be useless if I wasn't going to use the computer daily anyway.

Quite randomly, I somehow think making an imaginary company could be a good idea.
What I mean is setting up a bunch of computers/servers,
including stuffs like a public website, a intranet, employees' computers, firewalls, ... and a lot more.
Thanks to virtualization all can (theoretically) be hosted on that computer.

Despite seeming like a huge project, and I basically have 0 experience, I know I would also learn a lot during the process,
like virtualization, networking, sysadmin, probably some sick webdev css skill as well.
Realistic or not, I can only answer later but hey at least as bonus I can be the board and CEO, CSO, CTO at the same time.
Another side goal is too install as many different Linux/BSD distro as possible for fun.

Let's build a company.

### > Planning

The first thing to do to build a company is of course to come up with a name and business.
I was a big fan of [Phineas and Ferb](https://en.wikipedia.org/wiki/Phineas_and_Ferb) in my childhood.
I even brought a Doofenshmirtz t-shirt with me when going abroad.
So I am going to name my company Doofenshmirtz Evil Incorporated.
Also the company business is about making various "inator".

![1.jpg](/resources/1.jpg)

I will use either Linux or BSD for all computers involved, including the host itself and all the VMs.
The reason behind it is I'm more comfortable with UNIX command line, help me a lot with debugging and also Windows is not free.

For the network design of the company, I would need
- A firewall to isolate my computer from the internet and route inner connection

-> Learn about routing, subnets, ... and setup a firewall

- A company website, which is hosted on a public-facing server (not really public, just to the local physical server)

-> Design simple static website

- A company intranet, which is hosted on a not-so-public-facing server, can only be access from inside the company network

-> Design common productivity tools like calendar, notes, chatting, storage

- A computer for management (such as firewall configuration) a.k.a IT department
- An employee's computer, because a company should have at least 1 employee right

-> Simple computer installation

Also for future expansion, some ideas could be
- Expand computer intranet to a simple social network
- Network segmentation: Different departments (IT, marketing, finance, sales, HR,...), each department can not communicate directly to other departments.
- Setup remote access (SSH ?) to the intranet
- Setup a IPS/IDS
- Mimic an attack from outside, including port forwarding inside the intranet

### > Detailed design

![2.jpg](/resources/2.jpg)

I will choose [Linux Mint](https://www.linuxmint.com/) with XFCE to be the main OS of the machine because it is easily to install and relatively lightweight.
And I like its color scheme too (I still prefer Arch because no bloatwares though).

I will also use [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) for the hypervisor because it's free and easy to use.
Install VirtualBox with
```bash
sudo apt-get install virtualbox
```
We will optimize 3 modes of VirtualBox to setup the network (see more [here](https://www.virtualbox.org/manual/ch06.html))
- Bridged: Public <-> VM, Host <-> VM and VM1 <-> VM2
- Host-only: Host <-> VM, VM1 <-> VM2
- Internal: VM1 <-> VM2

For the network, we divide the network into 3 parts:
- Management Network: 172.20.0.0/24
- Public Servers: 172.21.0.0/24
- Intranet: 172.22.0.0/24

#### Firewall
- Everything should go through the firewall.
- It has 3 interfaces:
    + Bridged: Needed to connect directly to the public net (through host)
    + Host-only: Needed to connect to management network (which consists host)
    + Internal: Needed to connect to all other VMs
- Note that because I will include the host itself into the management network, so I couldn't use the bridged interface to connect to it, which means I need the host-only interface.
- Only host are allowed to config the firewall

#### Management Network
- Trusted network of the company (which might be the IT department in the future)
- Contains the hypervisor host (and SIEM - Security Intrusion Events Manager in the future)
- Used for configuring firewall

#### Public Server
- Contains public-facing server such as main website.
- Each has 2 interfaces:
    + Bridged: Needed to connect directly to the public net (through host)
    + Internal: Needed to connect to all other VMs

#### Intranet
- Contains the private intranet of the company.
- Each has 1 interface:
    + Internal: Needed to connect to all other VMs

### > The End
