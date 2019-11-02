---
layout: default
title: Project Incredibles
---
<h2 class="title text-center">cat plans-and-notes.txt</h2>

## FAQ
#### What is Project Incredibles ?
Project Incredibles is a small project of building a home lab, using virtualization on an old spare laptop.

#### What is this ?
This post is the megathread for all things related to Project Incredibles, including project roadmap, notes, documentations and more.

#### What is naming scheme for this ?
Incredibles is from the Pixar animated movie [The Incredibles](https://disney.fandom.com/wiki/The_Incredibles).
All of the machine are also be named after characters in the movie.

#### What is the goal of the project ?
The main goal is to learn about the basic infrastructure of an internal network, mostly for fun.
There are also several related benefits, for example setting up a DNS sinkhole to block ads, self-hosted email server or git/backup server.

#### What does the lab contain ?
I don't know yet. But I do have a sketch design of the lab, check the roadmap for details.
The spare laptop only has 4GB of RAM, so not enough to handle complicated system.

## Project Design
*Disclaimer*: I am using school Wifi to connect to the Internet, which is hidden behind NAT, so no actual public facing server here.
Whenever the word "public facing" is used, it means exposing the server to local school subnet.
However the concept is the same.

The internal network is consisted of several machines, all of which is built with virtualization on VirtualBox.

#### List of machines
- Elastigirl - Xubuntu 18.04.3 - Hypervisor
- MrIncredible - pfSense 2.4.4-p3 - Firewall/Gateway
- Dash - Ubuntu Server 18.04.3 - Nginx static webserver
- Violet - Ubuntu Server 18.04.3 - Apache PHP webserver
- JackJack - Ubuntu Server 18.04.3 - Git server
- Frozone - Ubuntu Server 18.04.3 - Email server
- Syndrome - Arch Linux - Main laptop (not VM)

#### Network
- Domain name: *.incredibles*.
- Every computer must use MrIncredible as DHCP server and DNS server, which acts as a DNS forwarder to school DNS server
- Internal network
    + Subnet: 172.16.1.0/24
    + Consists of most machines, including webservers, email server,...
    + All computers inside internal network can only connect to the internal network, not IT network or outside network.
    + All outbound connection (updating system for example) must go through Squid proxy of the gateway.
- IT network
    + Subnet: 172.16.2.0/24
    + Consists of machines for management reasons, including host hypervisor, IPS,...
    + Host hypervisor can connect to both internal network and IT network

#### Working from outside
- Temporary: Firewall can create an exception allowing Syndrome to connect directly to the internal network
- Permanently: Syndrome must use host hypervisor as a proxy to connect to internal network

## Project Roadmap
- Setup MrIncredible - Firewall/Gateway
    * ~~Install pfSense~~
    * ~~Network segmentation: 2 internal subnets, 1 public facing interfaces~~
    * ~~Setup DNS forwarder service~~
    * ~~Setup DHCP service~~
    * ~~Setup NTP service~~
    * ~~Setup Squid proxy service for 2 subnets~~
- Setup Dash - Nginx webserver
    * ~~Setup static IP in internal network and Squid proxy~~
    * ~~Setup Nginx webserver~~
    * Add static content
    * Add HTTPS support
- Allow internal access for other laptop - Syndrome
    * Setup static IP for MrIncredible and Syndrome
    * ~~Setup firewall fule for Syndrome~~
    * ~~(Temporary) Enable Squid proxy for Syndrome~~
    * Setup VPN for Syndrome
- Setup Violet - Apache webserver
    * Setup static IP in internal network and Squid proxy
    * Setup Apache webserver
    * Setup MySQL
    * Add dynamic content
    * Add HTTPS support
- Setup JackJack - Git server
    * Setup static IP in internal network and Squid proxy
    * Setup Gitea
    * Setup cronjob backup database of Violet
- Setup Frozone - Email server
    * Setup static IP in internal network and Squid proxy
    * Setup Postfix
    * (Not sure, maybe PosegreSQL + Dovecot as well)
- Further
    * Setup DNS sinkhole to block ads for MrIncredible (pfBlockerNG)
    * Change all devices in-house to use MrIncredible as DNS server

## Project Extension
There are many further extension of the lab that I want to do in the future.
- Indivial machines
    + Setup a SIEM server (Splunk)
    + Setup an IPS system (Snort)
    + Logging with ELK (Elasticsearch, Logstash, Kibana)
- Direction: Co-operative
    + Reverse proxy and load balancer for server
    + LDAP authentication
    + Active Directory with Windows machine
- Direction: Security
    + Setup a CTF server with harden security
    + Network with isolated machines to test malwares
    + Isolated vulnerable machines for pentesting

## Known Problem
- Host hypervisor (MrIncredible) is still accessible from the public Internet through wifi interface.
This wifi interface is also the bridged interface for the gateway so it is not possible to disable it.

## Credit
- Project Avatar of Tony Robinson a.k.a [da\_667](https://twitter.com/da_667): I take lot of inspiration and guidance from his book
- All Linux forum posts and Stackoverflow posts: For helping me with debugging various problems
