---
layout: post
title: Amazon Echo Hacking
categories: [Projects, Hacking]
tags: [tutorial, amazon,echo, iot, documentation, 3d print, root, linux]
img_path: /img/echo-hacking
---
# Overview:
I developed a habit of starting projects every winter break for the past few years. The following post documents this years project: **Rooting an Amazon Echo**
Easier said then done. The aim of my projects was to gain a better understanding of the inner workings of one and maybe even to modify it (perhaps even installing mycroft on it).
I started of by searching for known exploits and hit the jackpot. Apparently the first echo version (before 2016) has a security flaw where you can boot it into your own bootloader from an external sd card then set the correct boot aruguments for the actual boot partition and tada (all of that is done via a uart connection): 
One reboot later an you're presented with a fully functional root shell over uart. Furthermore you can boot into the diag partition with has some pretty fun tools/scripts on it (nothing really usefull unless your echo is damaged).

> ### TLDR:
I rooted my echo and copied all the system files from it. A howto is provided below. The following Repo contain all my tools and the System Files including short readme's:
* <https://github.com/Mich21050/EchoRooting>

But I already got ahead of myself. Let's begin step by step. In the follwing section I'm going to try to explain as best as I can the process of rooting your echo.
The process roughly looks like that:
1. solder SD-Card/uart to the test pads
2. flash SD-Card
3. boot to alternative bootloader
4. set boot arguments
5. root

# Howto:
### 1. Wiring
The follwowing "schematic" (credit to AndreRH) shows the two pinouts. In my case I was able to ommit two wires since I only need to establish an SPI connection (which need two less wires) inorder to intercept the boot process. The necessary data conncetions are the following:
* SDMMC D0 → MISO
* SDMMC D3 → !SS
* SDMMC CMD → MOSI
* SDMMC CLOCK → SCK

If you want to boot into an alternative Linux version (like descibred in [Andres Blog](https://andrerh.gitlab.io/echoroot/SETUP.html)) you need to connect all pads shown below.
The uart connection is a must an cannot be omitted since you have to manually install the ssh server (more on that later on):
![wiring](wiring.png)

I designed a small alternative base which just bolts into place instead of the original one. It has a small gap to make routing the wires easier and to be able to normally put it onto your desk (stl in repo):






> **Credits**:
This project woudln't have been able without f-secure's awesome blog post. Describing the exploit in detail and 
Andre's echoroot project. He did an awesome job giving me a rough overview of the inner workings. Furhermore I got a bunch of precompiled elf binaries from his echo image.
* <https://labs.f-secure.com/archive/alexa-are-you-listening/>
* <https://andrerh.gitlab.io/echoroot/>
