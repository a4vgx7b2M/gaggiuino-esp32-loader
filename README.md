# Important notice
It seems that ChatGPT is telling people to use this software on screens/modules from offical resellers. DO NOT DO THIS.

If you do this your screen/module will no longer boot. This is because the official hardware has encryption keys burned in, and flash encryption enabled at a hardware level. It will not (easily) boot an unencrypted image, which is what is contained in this repository.

If you ask for help through the official Discord, you will (incorrectly) be told that there is no way to "unbrick" your hardware, and if someone attempts to help you by providing these encrypted firmware files they will be banned. 

If you have bricked your official hardware using this repo, you might have a chance for repair if you follow the instructions here https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/tag/ChatGPTFix


# Gaggiuino Background
[Gaggiuino](https://gaggiuino.github.io/) is a project to add high-end features to Gaggia Classic espresso machines

The previous major software version (Gen2) was released under a CC-BY-NC license with all source code available. The most recent release (Gen3) is still released under a CC-BY-NC 4.0 license, but switched to a distrubution model where only the update binaries are available. You are required to purchase the hardware from an official supplier who will pre-flash the bootloader, partition table, and Gaggiuino software (with flash encryption enabled). The officially released hardware utilises an easily available ESP32-S3 smart display, the ESP32-8048S043. 

Prior to release v.616ea70, loading this software on any ESP32-S3-N16R8 was trivial. All that was required was a bootloader and a correct partition table pointing to ui-embedded.bin (Flashed to ota_0) and ui-web.bin (Flashed to an spiffs partition named webdata).

As of the current release (v.6655d6d) there are a number of significantly more involved security checks that require direct modification of the ui-embedded.bin binary. For ease of use, I have assembled the modified binary along with bootloader, partition table, ui-web.bin into a single 16MB binary that can be directly flashed at the 0x0 offset of the ESP32-8048S043. 

You can buy the ESP32-8048S043 from Aliexpress [Here](https://www.aliexpress.com/item/1005004788147691.html) 

**Make sure you buy the "IPS Capacitive touch" version (800*480 pixels), the others will not work**

## Disclaimer
I do not own the Gaggiuino project and am not affiliated with it in any capacity. The official Gaggiuino GitHub is linked above. All credit for the underlying project goes to zer0-bit/vsparxx and the rest of the Gaggiuino team. This project was simply out of interest and a desire to avoid purchasing from the official suppliers. This project is released in a non-commercial capacity and complies with the Gaggiuino CC-BY-NC 4.0 license

## Preparing to install
If coming from a pre-v.616ea70 release, then use the core OTA functionality to update your core to v6655d6d before you use these files to update your screen. This saves you from having to take apart your machine to update your STM32. I have reuploaded the STM32 binaries to this project's "Releases" from the official Github for ease of access and also as an archive (As old files were recently purged from the official Github). 

## Current Install Method (as of [3cbd9ab](https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/tag/v.3cbd9ab) Oct 15 2025)
Assuming you have updated to 616ea70 as described above, find the the three `bin` files for your machine from the [latest release](https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases/tag/v.3cbd9ab)

1. The Core Package (ex. performance-pcb-pca.bin, but refer to the official docs for which flavor you need) 
2. ui-embeded.bin
3. ui-web.bin

Then you can upload each bin to the respective uploading at gaggiuino.local on your machine.  Make sure to upload them in the order above (core, embeded, web), waiting for the screen to reboot each time.  You are [following the default install instructions](https://gaggiuino.github.io/#/guides-stm32/mcu-flashing?id=ota-esp32-stm32u585), just with the patched files.

## Legacy Software Installation
Note: This was relevent for earlier releases, but am including this if folks want to directly upload firmwares

**Be warned, there are parts of the binary designed to brick your screen if it doesn't pass the checks. I believe I have bypassed them all, and it works on my own hardware, but if you have a working screen consider if you really need the update.**

**Do not use OTA updates after flashing this. They will not work and may brick the screen**

Download the attached file named gaggiuino_full_v6655d6d.bin

The easiest to flash this is to use a web flashing tool [Like this one](https://web.esphome.io/). (Note that these flashers won't work in a Firefox based browser.)

<ins>Alternative installation method</ins>

The alternative and probably the technically correct method of flashing this file is to use esptool.py. This is installed as part of the ESP-IDF development environment [See here for setup details](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/).

Once you have installed esptool.py and it is active in your current terminal, simply run the following command:

esptool.py -b 921600 write_flash 0 gaggiuino_full_v6655d6d.bin

If the final command fails for you, you can try with different baud rates. The "-b 921000" portion can be replaced with "-b 460800", "-b 115200", or any other common baud rates. (921000 baud rate has been reported to fail on MacOS)

## Hardware Installation
Follow Gaggiuino installation guide for your planned setup<br/>
|Screen Pin|Blackpill Pin|
|----------|-------------|
|GPIO17|A3|
|GPIO18|A2|


## Headless Intallation
Similar to above, but flash gaggiuino_headless_v6655d6d.bin instead. Should work on any ESP32-S3-N16R8 module


## Conclusion
Unfortunately, OTA updates will no longer work, and may break you screen if you try an official OTA update. There is malicious code within the ui-embedded.bin binary that will set certain efuses and brick your screen to the point of having to desolder the ESP32-S3 module and attach a new one (I know this from personal experience)

I'll try to release a patched binary shortly after any major feature release, but may not update for minor changes. If there is a release that I have not yet patched then please open a Github issue and I'll see if I can patch it.

Asside from buying your screen from Aliexpress, there is also this [PCBv3](https://github.com/banoz/CoffeeHat/tree/main/Hardware/GaggiaBoard_V3) as an alternative to the PCBv3.1/PCBv4 from official suppliers. You would just need to also buy the WeAct STM32U585CIU6 from AliExpress. This would given you Gen3 software, without having to order through official suppliers so you can avoid their markups/shipping costs/shipping delays (this is what I did and where this project started).
