---
layout: post
title: "Flash Cisco AP from a Mac"
date: 2019-01-06 16:25:06 -0700
comments: false
---
This is for a Cisco AIR 3502.

1. Start off by installing the Tftp Server(http://ww2.unime.it/flr/tftpserver/oon your mac 
2. Acquire the Cisco Firmware that will be flashed on the access point.
3. Place the firmware in the ```/private/tftpboot/``` and start the tftp service
4. Change the permission ```chmod 744 /private/tftpboot/```and owner ```chown root:wheel /private/tftpboot```  
5. Connect a usb serial adapter to the mac and test the device with ```ls /dev/cu.usbserial ```or ```ls /dev/ttyUSB0```
6. On the mac open terminal and open a console to the access points with ```screen /dev/cu.usbserial 9600 cs8 -ixof```
7. Enter the username and password (hint: defaults are Cisco Cisco)
8. If you need to reset the device password press and hold the the mode button while the power is connected to the access point wait until the ap: prompt is displayed

The following commands will be run in the access point console
1. Set IP of the access point ```set IP_ADDR 10.0.0.20 ``` and netmask ```set NETMASK 255.255.255.0
 ```
2. Tell the AP the router's ip ```set DEFAULT_ROUTER 10.0.0.1 ```
3. Configure the AP to accept new firmware over tftp ``` tftp_init```
4. Start an ethernet connection to accept fireware```ether_init```
5. Allow for flash memory to be accessed ```flash_init```
6. Flash the ```tar -xtract tftp://<TFTP SERVER IP>/<FIRMWARE>.tar flash:``` an example ```tar -xtract tftp://<TFTP SERVER IP>/ap3g1-k9w7-tar.153-3.JF4.tar flash:```
7. Wait about 20 minutes, grab a coffee
8. Set the newly added firmware to boot```set BOOT flash:/ap3g1-k9w7-mx.153-3.JF4/ap3g1-k9w7-mx.153-3.JF4```
9. ```set MANUAL_BOOT no```
10. ```set boot```
11. Reboot the access point and see if the newly added firmware boots
12. Log back into the access point and delete the old firmware to free up space ```delete /force /recursive flash:<FIRMWARE>```
