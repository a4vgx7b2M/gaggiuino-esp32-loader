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
If coming from a pre-v.616ea70 release, make sure you OTA update your STM32 with the appropriate binary. I have reuploaded them to this projects "Releases" from the official Github for ease of access and also as an archive (As old files were recently purged from the official Github). 

## Software Installation
Download the attached file named gaggiuino_full_v6655d6d.bin

The easiest to flash this is to use a web flashing tool [Like this one](https://web.esphome.io/). (Note that these flashers won't work in a Firefox based browser.)

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
Information regarding headless will follow. 
The binaries are now different enough that ui-embedded.bin will not boot on a generic ESP32-S3-N16R8.
I focused on the screen as this is what I use, but in theory the same approach should work for both. I'll add a headless version soon if it does. 


## Conclusion
Unfortunately, OTA updates will no longer work, and may break you screen if you try an official OTA update. There is malicious code within the ui-embedded.bin binary that will set certain efuses and brick your screen to the point of having to desolder the ESP32-S3 module and attach a new one (I know this from personal experience)

I'll try to release a patched binary shortly after any major feature release, but may not update for minor changes. If there is a release that I have not yet patched then please open a Github issue and I'll see if I can patch it.

Asside from buying your screen from Aliexpress, there is also this [PCBv3](https://github.com/banoz/CoffeeHat/tree/main/Hardware/GaggiaBoard_V3) as an alternative to the PCBv3.1/PCBv4 from official suppliers. You would just need to also buy the WeAct STM32U585CIU6 from AliExpress. This would given you Gen3 software, without having to order through official suppliers so you can avoid their markups/shipping costs/shipping delays (this is what I did and where this project started).
