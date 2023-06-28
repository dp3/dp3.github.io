---
layout: post
title: "Wifi Talley Lights"
date: 2023-06-01 09:09:06 -0700
comments: false
---
This is a guide on how to flash the wifi talley for the https://wifi-tally.github.io/ project on Linux. 

**Flash and Configure the NodeMCU with Linux**
```
# Create Project Directory 
mkdir ~/wifiTalley

# Install the tools
git clone https://github.com/AndiDittrich/NodeMCU-Tool.git
sudo npm install nodemcu-tool -g
sudo npm install cli-progress  
pip install esptool

mkdir ~/wifiTalley/fireware
cd ~/wifiTalley/firmware/

# Download the binary file and config files 
wget https://github.com/wifi-tally/wifi-tally/releases/download/0.5.1/vtally-0.5.1-esp8266.zip
unzip vtally-0.5.1-esp8266.zip

# Flash the the Firmware 
esptool.py --port /dev/ttyUSB0 write_flash -fm dio 0x00000 ~/wifiTally/firmware/nodemcu-3.0-master_20200610-cfe68233-float.bin 

cd ~/wifiTally/NodeMCU-Tool

# Upload the config files
bin/nodemcu-tool.js upload ~/wifiTally/firmware/init.lua
bin/nodemcu-tool.js upload ~/wifiTally/firmware/*.lci

# edit the talley-settings.ini, set the ssid, ssid password, hub ip, and talley name 
vim ~/wifiTally/firmware/tally-settings.ini
 
# Upload the last config files 
bin/nodemcu-tool.js upload ~/wifiTally/firmware/tally-settings.ini

```
Resources
- https://wifi-tally.github.io/
- https://nodemcu.readthedocs.io/en/release/getting-started/#esptoolpy

