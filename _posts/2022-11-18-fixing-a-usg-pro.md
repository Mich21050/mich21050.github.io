---
title: Fixing a USG Pro
img_path: "/assets/img/usgpro"
---

# Overview
I've been using ubiquiti networking equipment in my homelab for over 2 years now and I'm very happy with it. It is extremly powerful and versaitile and easy to use at the same time. 
That said a friend of mine recently bought a broken usg pro for around 30€ and asked me if I would like to help him fix it. I said yes hoping to eventually fix it but mainly to learn how to diagnose and work with embedded linux devices.
# First diagnose
The usg like the majority of network equipment has a RJ-45 RS232 port on the front side of the switch. 
![USG Front View](usg_front.jpg)
The following approach is kinda dangerous and not that smart but I wanted to start debugging it so in the end this was my only option.
I opened it up and voaily, right next to the RJ-45 jack sits a [MAX3221 ](https://www.ti.com/lit/ds/symlink/max3221.pdf?ts=1668689257495&ref_url=https%253A%252F%252Fwww.google.com%252F) RS232 line driver. Excatly what I was hoping to see. 
If you're not familiar with what a line driver is, it's a simple logic level converter which converts TTL Serial (most of the time 3.3V) to the stanard RS232 Voltage of 5V. 
Now using that knowledge we can just solder two cables to the according pins of the IC (if for some reason you want to replicate this approach the datasheet is linked abvove).
My ground was just a pin on the fan header but there are many other (nicer) possibilities to get a common ground.
My final contraption:
