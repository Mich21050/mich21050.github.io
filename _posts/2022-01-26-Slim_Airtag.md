---
title: Slim Airtags
date: 2022-01-26 19:26:00 -0100
categories: [Projects, Modification]
tags: [tutorial, apple, documentation, 3d pritn]
img_path: /_img/airtag
---
I have been using Airtags to track my keys for a few weeks now and I absolutely love them. They have saved me on numerous occasions from forgetting or even losing my keys. My only problem with them is that they are too thick for my Secird Slim Wallet. With my whole wallet being 21mm thick and the Airtags being 8mm they are simply too big to fit. So I set out to build my own thinner Airtag. After a quick google search, I stumbled upon the video from [Andrew Ngai](https://www.youtube.com/watch?v=7rHyAAkf5tE) and decided I would adopt his approach. First of all, I disassembled one of my Airtags:

As you can see the PCB and the Antenna are sandwiched with the case on one side and a battery on the other. I decided to use Andrew's design but I slightly modified it. My main concern was that there was no cover on either side to protect the electronics. So I modified [his design](https://www.thingiverse.com/thing:4850601) to incorporate a two-layer thick bottom cover. In order to reduce the height and maintain serviceability, I made the cover out of duct tape. While it may not be beautiful, I've been using exactly that solution for the last few months and it worked perfectly.
Design:

![the new airtag housing](3d_overview.png)

> I ended up printing it using white PLA+.

The disassembly is pretty straightforward. There are two approaches too disassembling. I choose the more destructive but also quicker way since the other one didn't work and I ended up destroying one airtag.... oops :)

Start by prying off the battery cover:

Then just start cutting away the thin plastic on the backside. I just used a pair of small pliers. Just start by cutting into it in the middle and start pulling and cutting away the plastic.

Once all the white plastic is removed you should have the antenna/PCB module. Be careful to not the antenna on the back (the small gold trace).

Now you need to solder one wire to each of the contacts marked below. The polarity is marked in the picture.

After that take a small piece of wire and connect the other two contacts below.

The result should look something like this:


The final result should look like this:

Now your Airtag should be discoverable and you should be able to connect to it. Once you made sure everything is working (except the speaker) you can seal it up by using a small piece of duct tape. 

And that's it. You just made your very own airtag. Everything except the speaker should work and the battery is easily replaceable. If you have any questions don't hesitate to contact me :)

TODO:
* v2 with working speaker