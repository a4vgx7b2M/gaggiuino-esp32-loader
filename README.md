# Gaggiuino Loader

This project allows the Gaggiuino software to be flashed to hardware that is purchased from retailers other from the official suppliers

I have released this as a web flashing utility for ease of use

Simply click the link below and follow the instructions

[a4vgx7b2M.github.io](https://a4vgx7b2M.github.io)


## Required hardware

You will need an ESP32-8048S043 for the screen version, or any ESP32-S3-N16R8 module for the headless version

You can buy the ESP32-8048S043 from Aliexpress [Here](https://www.aliexpress.com/item/1005004788147691.html) 

Make sure you buy the "IPS Capacitive touch" version (800*480 pixels), the others will not work


## Updating
On your screen or webUI, go to Settings -> Firmware Update -> Update all firmware

This will update your core and screen/headless module to the latest patched version

Latest embedded UI and performance core firmware: a06f97fd

Latest web UI: 2e8c114

<br><br><br><br><br><br>
## Important notice
It seems that ChatGPT is telling people to use this software on screens/modules from offical resellers. DO NOT DO THIS.

If you do this your screen/module will no longer boot. This is because the official hardware has encryption keys burned in, and flash encryption enabled at a hardware level. It will not (easily) boot an unencrypted image, which is what is contained in this repository.

~~If you ask for help through the official Discord, you will (incorrectly) be told that there is no way to "unbrick" your hardware, and if someone attempts to help you by providing these encrypted firmware files they will be banned.~~

~~If you have bricked your official hardware using this repo, you might have a chance for repair if you follow the instructions here https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/tag/ChatGPTFix~~

Zer0-bit has now added this functionality to his [web emergency restore tool](https://gaggiuino.github.io/emergency_restore.html). Use this to repair your official hardware if you have accidentally flashed these files to it. 

## Hardware Installation
Follow Gaggiuino installation guide for your planned setup<br/>
|Screen Pin|Blackpill Pin|
|----------|-------------|
|GPIO17|A3|
|GPIO18|A2|


## Headless Intallation
Similar to above, but select headless instead of screen at the flashing stage. Should work on any ESP32-S3-N16R8 module


## Disclaimer
I do not own the Gaggiuino project and am not affiliated with it in any capacity. The official Gaggiuino GitHub is linked above. All credit for the underlying project goes to zer0-bit/vsparxx and the rest of the Gaggiuino team. This project was simply out of interest and a desire to avoid purchasing from the official suppliers. This project is released in a non-commercial capacity and is in compliance with the Gaggiuino CC-BY-NC 4.0 license
