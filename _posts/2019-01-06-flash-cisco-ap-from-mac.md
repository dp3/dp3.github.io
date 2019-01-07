---
layout: post
title: "Flash Cisco AP from a Mac"
date: 2019-01-06 16:25:06 -0700
comments: false
---
This is for a Cisco AIR 3502.

Start off by installing the Tftp Server(http://ww2.unime.it/flr/tftpserver/oon your mac 
Acquire the Cisco Firmware that will be flashed on the access point.
Place the firmware in the ```/private/tftpboot/``` and start the tftp service 
Connect a usb serial adapter to the mac and test the device with ```ls /dev/cu.usbserial ```or ```ls /dev/ttyUSB0```
On the mac open terminal and open a console to the access points with ```screen /dev/cu.usbserial 9600 cs8 -ixof```
Enter the username and password (hint: defaults are Cisco Cisco)
If you need to reset the device password press and hold the the mode button while the power is connected to the access point wait until the ap: prompt is displayed

The following commands will be run in the access point console

