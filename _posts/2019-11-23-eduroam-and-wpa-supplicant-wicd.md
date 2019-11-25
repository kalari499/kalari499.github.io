---
layout: post
title: Tips&#58; Using wpa_supplicant / wicd with eduroam (and wifi router at dorm)
description: How to connect to Polytechnique Wifi eduroam with Linux with wpa\_supplicant and wicd. Including a quick note on setting up Wifi inside dorm.
category: Tips
---

### Context

eduroam is very cool.
For many schools the logging in process for Wifi is pretty simple.
Unfortunately Polytechnique is not included in the "pretty simple" category.
It's pretty painful to connect to eduroam Wifi here, especially on Linux.
Mostly because everybody is using Windows/MacOS so there aren't not many support available.
And the document are crazily minimal and unhelpful.

Anyway, first if you are using NetworkManager, say Ubuntu for example, then you are lucky.
The process is actually quite easy.

This post is for those who are using wicd as network manager (default of Devuan and more), or
in some other weirder cases, wpa\_supplicant.
Also if you planning to setup an OpenWRT router in the school (not very recommended), you will need this.
This is for you Abdel.

Of course, even if you are not from Polytechnique, maybe you can still see something similar to your school.


### Magical wpa\_supplicant config

I should have mentioned this sooner but here we are.
Polytechnique wifi is using authentication method Tunneled TLS (TTLS) followed by Password Authentication Protocol (PAP).

Guess which method of authentication wicd does not support out-of-the-box ? TTLS-PAP

There are hardly any decent documentations out there for wicd.
But anyway after some painful testing I finally came up with a way to add support for it so you don't have to.

wicd is essentially a wrapper of wpa\_supplicant.
It has some out-of-the-box templates for authentication methods, located in `/etc/wicd/encryption/templates/`.
So we need to add our own.
```bash
sudo sh -c 'cat << EOF > /etc/wicd/encryption/templates/eduroam
name = eduroam - TTLS with PAP
author = kalari499
version = 1
require identity *Identity password *Password
protected password *Password
-----
ctrl_interface=/var/run/wpa_supplicant
network={
  ssid="$_ESSID"
  key_mgmt=WPA-EAP
  eap=TTLS
  identity="$_IDENTITY"
  anonymous_identity="anonymous@polytechnique.fr"
  password="$_PASSWORD"
  phase2="auth=PAP"
}
'
```

Restart wicd with
```
sudo systemd restart wicd
OR
sudo /etc/init.d/wicd restart
```

Now you can connect to the eduroam by choosing the selection "eduroam - TTLS with PAP".
Some notes:
- `key_mgmt=IEEE8021X` something might work but I haven't tested it.
- Change `anonymous_identity` to something different if you're in other school.
- (Note for Abdel) `"` is highly important. That's what might cost you few weeks debugging. You would see parsing error otherwise.


### If you just use wpa\_supplicant

If you are crazy and only use plain wpa\_supplicant, create a script in `/etc/wpa_supplicant/`
```bash
sudo sh -c 'cat << EOF > /etc/wpa_supplicant/eduroam.conf
ctrl_interface=/var/run/wpa_supplicant
network={
  ssid="eduroam"
  key_mgmt=WPA-EAP
  eap=TTLS
  identity="your.polytechnique.username@polytechnique.fr"
  anonymous_identity="anonymous@polytechnique.fr"
  password="your.polytechnique.password"
  phase2="auth=PAP"
}
'
```

Of course the rest you should already know.
To connect to the wifi run
```
wpa_supplicant -i wlp2s0 -c /etc/wpa_supplicant/eduroam.conf
```

And to do automatic DHCP
```
dhcpcd wlp2s0
OR
dhclient -v wlp2s0
```
(change wlp2s0 to wlan0 or whatever interfaces you are using).


### OpenWRT router in the dorm

Once again, this is not recommended because of security reasons, do it at your own risk and proceed with caution.
I'm just making a quick guideline here.

Network hole in Polytechnique is not something you can just the cable in and get the internet.
You need to authenticate yourself to the school first, which of course is not supported in most (or all) commercial routers out there.

OpenWRT is a Linux distribution for embedding systems, particularly routers.
We will be using this for our router.
- Step 0: Get yourself a router that OpenWRT supports. See the list [here](https://openwrt.org/supported_devices). Something like TPLink Archer should work.
- Step 1: Plug in the wall cable to the router. Connect your computer to the router (no Internet yet).
- Step 2: Go to web portal of OpenWRT (192.168.1.1 or 192.168.0.1 something), perform Firmware Update and flash the binary OpenWRT update you downloaded.
- Step 3: SSH to the router (don't forget to change password). Create a wpa\_supplicant config file like above and connect. No need to run DHCP service. Also enable wpa with `/etc/init.d/wpa enable`.
- Step 4: Add static IP with `uci`. Protocol: static, Gateway: 129.104.244.254, DNS: 129.104.201.{59,51} or something similar.
- Step 5: Setup date, firewall, authentication methods, etc

Good luck installing this.
I promise you would learn a lot doing the process.
If you fuck up at any point you can always factory reset the router and try again.


### The End

That's it for this post, see you next time.
