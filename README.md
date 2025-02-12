# Gaggia ESP32S3 Bootloader and Partition Table
[Gaggiuino](https://gaggiuino.github.io/) is "a community-driven project to add high-end features to Gaggia Classic espresso machines"

The previous major software version (Gen2) was released under a CC-BY-NC license with all source code available. The most recent release (Gen3) is still released under a CC-BY-NC 4.0 license, but switched to a distrubution model where only the update binaries are available. You are required to purchase the hardware from an official supplier who will pre-flash the bootloader, partition table, and Gaggiuino software (with flash encryption enabled). The officially released hardware utilises the easily available ESP32-S3 smart display, the ESP32-8048S043. There is also a "headless" version which runs on an ESP-32-S3-WROOM-1 mounted on a custom PCB.

The only thing preventing you from running the Gaggiuino Gen3 software on an ESP32-S3 purchased from a third party is the pre-flashed bootloader and partition table. This project gives the information and files required to build a compatible bootloader and partition table, allowing this software to be successfully run on any compatible device. (Please note if you are purchasing an ESP32-8048S043, you need to buy the IPS capacitive touch 800*480 version. If you are purchasing an ESP-32-S3 then you need the N16R8 version)

## Disclaimer
I do not own the Gaggiuino project and am not affiliated with it in any capacity. The official Gaggiuino GitHub is linked above. This project was simply out of interest and a desire to avoid purchasing from the official suppliers. This project is released in a non-commercial capacity and complies with the Gaggiuino CC-BY-NC 4.0 license

## Bootloader
The official Gaggiuino Gen3 bootloader has flash encryption enabled but not secure boot. If a generic bootloader is built, this will allow loading of the publicly available binaries

## Partition layout
Detailed information about the overall ESP-32 partition scheme is available elsewhere. With respect to Gaggiuino specifically, the ui-embedded.bin is flashed to the ota_0 (or ota_1) partition, which runs the main software. The webdata partition holds a littlefs filesystem containing the data for the embedded webserver. The below partition table (included in the attached .csv file) allows booting of the officially released binaries and at present also allows OTA updates via the built in updater

|Name|Type|SubType|Offset|Size|Flags|
|----|----|-------|------|----|-----|
|nvs|data|nvs|0x9000|0x4000|
|otadata|data|ota|0xd000|0x2000|
|phy_init|data|phy|0xf000|0x1000|
|ota_0|app|ota_0|0x10000|0x400000|	
|ota_1|app|ota_1|0x410000|0x400000|
|webdata|data|spiffs|0x810000|0x3f0000|	
|tempdata|data|spiffs|0xc00000|0x400000|

## Build
Skip to "Software Installation" below if you intend to use the pre-compiled binaries available for download from [Releases](https://github.com/a4vgx7b2M/gaggiuino-esp32-loader/releases)

You will require an installed and active esp-idf environment [See here for setup details](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/). You will also require the file partitions.csv downloaded from this repository

Run the below commands to build from source on Linux. Windows will have different commands and a different build environment, but will also be able to build these files from source

```
idf.py create-project gaggia-esp32
cd ./gaggia-esp32
idf.py set-target esp32s3
idf.py menuconfig
```
Here we will configures specific bootloader parameters. Change the following parameters using menuconfig:

```
Serial flasher config --> Flash SPI mode --> QIO
Serial flasher config --> Flash size --> 16 MB
Partition table --> Partition Table --> Custom partition table CSV
Press Q then Y to exit and save changes
```
We will then copy partitions.csv (Our own partition table) to current directory, then build the required files "bootloader.bin" and "partition-table.bin"

```
cp /path/to/downloads/gaggiuino-esp32-loader-x.x.x/partitions.csv ./partitions.csv
idf.py build
```

## Software Installation

|You will need:||
|--------|-----|
|esptool.py|Should install automatically with esp-idf installation|
|bootloader.bin|./build/bootloader/bootloader.bin if building from source, or copy from Releases|
|partition-table.bin|./build/partition_table/partition-table.bin if building from source, or copy from Releases|
|ui-embedded.bin|https://github.com/Zer0-bit/gaggiuino/releases|
|ui-web.bin|https://github.com/Zer0-bit/gaggiuino/releases|

We will now copy all 4 of the above .bin files into a single directory, then enter this directory and flash these files. (Note that the below commands assume you are running them directly after building the above files. If you have closed your terminal, you will need to ensure your current terminal is running from the correct folder and you have re-activated your esp-idf environment.)

If you are flashing from the pre-compiled binaries, replace ./build/bootloader/bootloader.bin and ./build/partition_table/partition-table.bin in the below commands with the appropriate paths to the downloaded binaries

```
mkdir ./toflash
cp ./build/bootloader/bootloader.bin ./toflash/bootloader.bin
cp ./build/partition_table/partition-table.bin ./toflash/partition-table.bin
cp /path/to/downloads/ui-embedded.bin ./toflash/ui-embedded.bin
cp /path/to/downloads/ui-web.bin ./toflash/ui-web.bin
cd ./toflash
esptool.py -b 921600 -p /dev/ttyUSB0 write_flash 0 bootloader.bin 0x8000 partition-table.bin 0x10000 ui-embedded.bin 0x810000 ui-web.bin
```
If the final command fails for you, you can try with different baud rates. The "-b 921000" portion can be replaced with "-b 460800", "-b 115200", or any other common baud rates. This portion can even be completely removed but will result in slower flashing. 
(921000 baud rate has been reported to fail on MacOS)


## Hardware Installation
Follow Gaggiuino installation guide for your planned setup<br/>
|Screen Pin|Blackpill Pin|
|----------|-------------|
|GPIO17|A3|
|GPIO18|A2|


## Headless Intallation
A headless install also works. All you need is an ESP-32-S3-N16R8, the devkit works and should be easily available on Amazon/Aliexpress/other. Flashing either ui-embedded.bin or ui-headless.bin works, but for simplicity I have flashed ui-embedded.bin as I had some OTA issues with ui-embedded.bin. You can get SD shot history working by wiring the using the following connections:
|SD Pin|ESP32-S3 Pin|
|------|------------|
|1|+3.3V|
|2|GPIO10|
|3|GPIO11|
|4|+3.3V|
|5|GPIO12|
|6|GND|
|7|GPIO13|
|8|+3.3V|
|9|NC|


## Conclusion
The screen functions identically to those activated by official suppliers at time of writing. It also works with OTA updates (including U585 core updates), however I cannot guarantee this will continue to work in future

This software can be installed on any ESP32-8048S043 from Aliexpress (Just make sure you get the IPS capacitive touch 800*480 version). Also as far as I have tested it will also run on any ESP32-S3 with 16MB flash (required for ui-embedded) and 8MB PSRAM (not tested with <8MB)

Combine this with [PCBv3](https://github.com/banoz/CoffeeHat/tree/main/Hardware/GaggiaBoard_V3) (or order from a 3rd party/group buy) and the WeAct STM32U585CIU6 from AliExpress, and you can have Gen3 software, OTA updates including Core updates, and STM32U585 performance, all without having to order through official suppliers so you can avoid their markups/shipping costs/shipping delays
