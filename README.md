# Gaggiuino Loader

This project allows the Gaggiuino software to be flased to hardware that is purchased from retailers other from the official suppliers

I have released this as a web flashing utility for ease of use. If you are coming from an older version of this project you should update using this utility as this will fix bluetooth

Simply click the link below and follow the instructions

[a4vgx7b2M.github.io](https://a4vgx7b2M.github.io)


## Required hardware

You will need an ESP32-8048S043 for the screen version, or any ESP32-S3-N16R8 module for the headless version

You can buy the ESP32-8048S043 from Aliexpress [Here](https://www.aliexpress.com/item/1005004788147691.html) 

Make sure you buy the "IPS Capacitive touch" version (800*480 pixels), the others will not work


## Updating
Assuming you have used the web flash utility at least once, you will be able to perform OTA updates moving fowards

Find the the three `bin` files for your machine from the [latest patched release](https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/latest)

Do not use the officially released update binaries as they will break your screen. If this happens you will have to use the web flashing tool again

1. The Core Package (ex. performance-pcb-pca.bin, but refer to the official docs for which flavor you need) 
2. ui-embeded.bin (for screens) or ui-headless.bin (for headless modules)
3. ui-web.bin

Then you can upload each bin to the "SETTINGS" tab "OTA Update" section available at [gaggiuino.local](http://gaggiuino.local/)

Make sure to upload them in the order listed above (core, embeded, web), waiting for the screen to reboot each time.

You are [following the default install instructions](https://gaggiuino.github.io/#/guides-stm32/mcu-flashing?id=ota-esp32-stm32u585), just with the patched files.


## Important notice
It seems that ChatGPT is telling people to use this software on screens/modules from offical resellers. DO NOT DO THIS.

If you do this your screen/module will no longer boot. This is because the official hardware has encryption keys burned in, and flash encryption enabled at a hardware level. It will not (easily) boot an unencrypted image, which is what is contained in this repository.

If you ask for help through the official Discord, you will (incorrectly) be told that there is no way to "unbrick" your hardware, and if someone attempts to help you by providing these encrypted firmware files they will be banned. 

If you have bricked your official hardware using this repo, you might have a chance for repair if you follow the instructions here https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/tag/ChatGPTFix


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
