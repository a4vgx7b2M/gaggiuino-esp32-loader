# Gaggia ESP32S3 Bootloader and Partition Table
Gaggiuino is a modification to a popular coffee machine (Gaggia Classic) which adds multiple improvements over the original machine (https://github.com/Zer0-bit/gaggiuino)

The previous major software version (Gen2) was released under a CC-BY-NC license with all source code available. The most recent release (Gen3) is still released under a CC-BY-NC 4.0 license but switched to a distrubution model where only the update binaries are available, and requires purchase of the hardware from an official supplier who will pre-flash the bootloader, partition table, and Gaggiuino software (with flash encryption enabled). The officially released hardware utilises the easily available ESP32-S3 smart display, the ESP32-8048S043

This software allows you to build a compatible bootloader and partition table, meaning you will be able to run the Gen3 software on any ESP32-8048S043 you are able to source (and theoretically on any ESP32-S3 N16R8)

## Disclaimer
I do not own the Gaggiuino project and am not affiliated with it in any capacity. The official Gaggiuino GitHub is linked above. This project was simply out of interest and a desire to avoid purchasing from the official suppliers. This project is released in a non-commercial capacity and complies with the Gaggiuino CC-BY-NC 4.0 license

## Bootloader
The official Gaggiuino Gen3 bootloader has flash encryption enabled but not secure boot. A simple bootloader without flash encryption enabled functions perfectly with the current software release version (v.0e28389)

## Partition layout
Detailed information about the ESP-32 partition scheme is available elsewhere. The below partition table allows booting of the officially released binaries and OTA updating of these binaries

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
Skip to "Software Installation" below if you intend to use the pre-compiled binaries available for download from [Releases](https://github.com/a4vgx7b2M/gaggia-esp32/releases)

Below commands are those required to build from source on Linux. Windows will have different commands but will also be able to build from source

To build from source you require an installed and activate esp-idf environment (https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/), as well as partitions.csv downloaded from this repository

```
idf.py create-project gaggia-esp32
cd ./gaggia-esp32
idf.py set-target esp32s3
idf.py menuconfig
```

Change the following menuconfig parameters

```
Serial flasher config --> Flash SPI mode --> QIO
Partition table --> Partition Table --> Custom partition table CSV
Press Q then Y to exit and save changes
```
Copy partitions.csv to current directory and build bootloader.bin and partition-table.bin

```
cp /path/to/downloads/gaggia-esp32-x.x.x/partitions.csv ./partitions.csv
idf.py build
```

## Software Installation
You will need:<br/>
esptool.py&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(Should install automatically with esp-idf installation)<br/>
bootloader.bin&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(./build/bootloader/bootloader.bin if build from source, or copy from Releases)<br/>
partition-table.bin&ensp;&ensp;&ensp;&ensp;&ensp;(./build/partition_table/partition-table.bin if build from source, or copy from Releases)<br/>
ui-embedded.bin&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(https://github.com/Zer0-bit/gaggiuino/releases)<br/>
ui-web.bin&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(https://github.com/Zer0-bit/gaggiuino/releases)

Copy all 4 of the above .bin files into a single directory, then enter this directory and flash these files. (Note that these commands are assumed to be run directly after building the above files. If you have closed your terminal in the interim, please ensure your current directory in terminal is gaggia-esp32, and you have re-activated your esp-idf environment for the active terminal session. If you are flashing from the pre-compiled binaries, replace ./build/bootloader/bootloader.bin and ./build/partition_table/partition-table.bin in the below commands with the appropriate paths to the downloaded binaries)

```
mkdir ./toflash
cp ./build/bootloader/bootloader.bin ./toflash/bootloader.bin
cp ./build/partition_table/partition-table.bin ./toflash/partition-table.bin
cp /path/to/downloads/ui-embedded.bin ./toflash/ui-embedded.bin
cp /path/to/downloads/ui-web.bin ./toflash/ui-web.bin
cd ./toflash
esptool.py -b 921600 -p /dev/ttyUSB0 write_flash 0x1000 bootloader.bin 0x8000 partition-table.bin 0x10000 ui-embedded.bin 0x810000 ui-web.bin
```

Theoretically a headless install also works. Simply download ui-headless.bin and replace ui-embedded.bin with ui-headless.bin above (Note that I have not tested this extensively, other than ensuring it boots and connects to my blackpill u585)

## Hardware Installation
Follow Gaggiuino installation guide for your planned setup<br/>
Connect screen GPIO17 --> Blackpill A3<br/>
Connect screen GPIO18 --> Blackpill A2<br/>

## Conclusion
The screen functions identically to those activated by official suppliers at time of writing (Release v.0e28389). It also works with OTA updates (including U585 core updates) as of time of writing, however I cannot guarantee this will continue to work in future

This software can be installed on any ESP32-8048S043 from Aliexpress, and likely any ESP32-S3 with 16MB flash (required for ui-embedded) and 8MB PSRAM (not tested with <8MB)

Combine this with [PCBv3]([https://www.pcbway.com/project/shareproject/GaggiaBoard_V3_6c90dfac.html](https://github.com/banoz/CoffeeHat/tree/main/Hardware/GaggiaBoard_V3) (or order from a 3rd party/group buy) and the WeAct STM32U585CIU6 from AliExpress, and you can have Gen3 software, OTA updates including Core updates, and STM32U585 performance, all without having to order through official suppliers so you can avoid their markups/shipping costs/shipping delays
