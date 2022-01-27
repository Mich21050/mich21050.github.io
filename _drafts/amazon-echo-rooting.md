---
layout: post
title: Amazon Echo Rooting
categories:
- Projects
- Hacking
tags:
- tutorial
- amazon
- echo
- iot
- documentation
- 3d print
- root
- linux
img_path: "/img/echo-root"
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
## 1. Wiring
The follwowing wiring diagram (credit to AndreRH) shows the two pinouts. In my case I was able to ommit two wires since I only need to establish an SPI connection (which need two less wires) inorder to intercept the boot process. The necessary data conncetions are the following:
* SDMMC D0 → MISO
* SDMMC D3 → !SS
* SDMMC CMD → MOSI
* SDMMC CLOCK → SCK

If you want to boot into an alternative Linux version (like descibred in [Andres Blog](https://andrerh.gitlab.io/echoroot/SETUP.html)) you need to connect all pads shown below.
The uart connection is a must an cannot be omitted since you have to manually install the ssh server (more on that later on):
![wiring](wiring.png)
_Wiring Diagram_

## 2. New base
I designed and printed a small alternative base which just bolts into place instead of the original one. It has a small gap which makes routing the wires easier.
![base](base.jpg){: width="700" height="400" }
_3d printed base_

## 3. Flashing SD-Card
Pretty easy process. I used this [image](https://github.com/echohacking/wiki/wiki/Echo) from the echohacking repo and just used the dd command like so. You could also use something like balenaEtcher but I would recommend using Linux for the whole process (a VM is sufficient).
```shell
dd if=debian-sd.img of=/dev/sdX bs=8M; sync
```
You now could use something like fdisk to delete the unused partitions (all linux partitions) but you don't have too since the echo won't be able to boot from them anyway.

## 4. Booting the echo
Now comes the fun part. Booting your echo for the first time. Plug in your uart converter. Open a connection with a baud rate of 115200 (e.g putty), insert your sd card into the reader now plug in the power supply and pray. If everything worked you should now be able to hit enter and you should be presented with a uboot command line.
If thats the case congrats! :)

If you want to make your life  easy just use my rootEcho.sh script (you only need to change the active partition).
The other option is to enter the commands per hand as described below:
```shell
#list all mmc partitions
mmc dev l
# we now need to determine the active partiotion
# just list the root folder of both possible partitions; one should contain a valid file system (pretty easy to recognize/should have boot.bin)
ext4ls mmc 1:6
ext4ls mmc 1:7
# now select the correct paritition to edit
setenv mmc_part 1:x # x= 6/7
setenv mmc_part /dev/mmcblk1pX # x again reperesentin the active partition
# run /bin/sh at startup
setenv mmcargs 'setenv bootargs console=${console} root=${root} ${mount_type} rootfstype=ext3 rootwait ${config_extra} init=/bin/sh'
setenv mount_type rw
# boot echo
boot
```
Once the echo has booted a root terminal is presented over UART.
```shell
sh-3.2# whoami
root
```
Since none of the normal initialisation scripts have been ran the device would reboot every few minutes. To prevent that just spawn the watchdog daemon.
```shell
/usr/local/bin/watchdogd
```
You just rooted your echo. In the next step we're going to install a simple ssh server.

## 5. Activating wifi
We now need to bring up the wifi interface in order to download the ssh server. To do that just execute the following command:
```shell
#wifi setup commands here
```

## 6. SSH Server
First of all we need to download the ssh and sshd binaries. The easiest way in my experience is to just setup a simple ftp server on your machine and then just use wget to download the ssh directory. You can just download it to the echo's home directory and delete the whole folder after installing it.
Before proceeding with the installtion we need to set a password for the root user since the ssh server won't start without one.
```shell
# set password for root user
passwd
```

Now run the ssh install script and you should be almost done. The script copies all the relevant files to their respective locations. 
We now need to add the correct ip table rule:
```shell
# ip table rule
```

The last step is to add the sshd start command to one of the startup scripts. I just appended it to the varlocal.sh script since it's run at the end of the boot process so everything the ssh server needs should be up and running.
Just append the following command:
```shell
# start command
```
You can test the ssh server by starting it right now:
```shell
#starts ssh Server
```
Now try to connect to your echo. You should be able to log in as root with the above set password.
Once you verified that your ssh server is up and running you can reboot the echo.
Simply remove the micro SD-Card from the reader and reboot the echo (just disconnect and reconnect the power cable).

## 7. The inner workings
Your echo is basically just a simple linux computer with runs a few programs/daemons to enable the actuall alexa on it. 
They all communicate on a virtual bus called __lipc__ which is basically a propiertary data format for the standard linux dbus. Due to that we can just use the dbus-monitor command to listen to all data sent over the bus:
```shell
dbus-monitor --system
```
You could now, for example, mute your echo and monitor the messages over the bus. 
But it gets even better then that. The folks over at lab126 were kind enough to leave all of the lipc helper programs on the echo. So just type lipc and press tab two time and you're going to get a list of all available commands. Using them is really easy since they all take the --help argument.

One really nice command is the lipc-discover commands. It takes a few arguments which are all described in the help section of it. It's going to take around 30 seconds to run but once it's done it returns a list of all lipc endpoints with their properties and methods. 
A copy of the command output can be found in my [repo](https://github.com/Mich21050/EchoRooting/blob/main/dbusDebug/lipDisc.txt). 


Don't be afraid to poke around your echo. You can't really damage anything so just try various lipc commands. A example lipc-send command is described below. 

# Working with your echo
## 1. LED Ring




> **Credits**:
This project woudln't have been able without f-secure's awesome blog post. Describing the exploit in detail and 
Andre's echoroot project. He did an awesome job giving me a rough overview of the inner workings. Furhermore I got a bunch of precompiled elf binaries from his echo image.
* <https://labs.f-secure.com/archive/alexa-are-you-listening/>
* <https://andrerh.gitlab.io/echoroot/>
