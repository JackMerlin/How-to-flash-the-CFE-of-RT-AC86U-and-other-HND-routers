# How to flash the CFE of RT-AC86U and other HND routers

The partition structure of the Broadcom ARM64 chip (HND platform)  is different from that of the previous ARMv7, so the function of the partition cannot be judged only from the name of the partition label.

Usually, the CFE of Broadcom ARM64 chip consists of three parts: `cfe`, `cfe_ram` and `cfe_rom`. I haven't figured out the role of the three parts, but they are all necessary.

But one thing is certain, the `mtd0` partition is still the `u-boot` partition, but it is not the only CFE partition. generally, the complete CFE has three partitions.

But from the experience of analyzing multiple routers of multiple brands, the `mtd0` partition is named `nvram`, but in fact this partition has nothing to do with the nvram we refer to, the real nvram save location It is specified by the firmware manufacturer. Different manufacturers have different nvram locations and storage methods.

Before starting, first emphasize the importance of the variables in `nvram` for Broadcom chip routers: Since Broadcom radios require nvram to provide some configuration variables, the wrong configuration of nvram variables will directly cause WiFi to stop working. 


## Entering the topic

The following is the location and method of storing the factory nvram for routers of Asus HND platform (including RT-AC86U and later AX series):

asuswrt stores the `factory nvram` in the `misc1` partition, which is called `mtd10` partition in the firmware

asuswrt stores the `temporary nvram` (user writable and configurable) in the `misc2` partition, which is called `mtd9` partition in the firmware.

## Here's the point: How to modify variables in `misc1` partition?

1. Back up the `mtd10 partition` with the `dd` command and name it `misc1.img`

2. `misc1.img` is an image in UBIFS (Unsorted Block Image File System) format, mount it to get the `nvram.nvm` file inside

3. The `nvram.nvm` file is converted from the `nvram.txt` plain text file, and the conversion command is: `nvserial -a -s 100 -o $(DFT_NVRAM_NAME)_nvram.nvm $(DFT_NVRAM_NAME)_nvram.txt`

4. If you want to restore `nvram.nvm` to `nvram.txt`, you need to convert the delimiter in the hexadecimal byte stream

5. After modifying `nvram.txt` and converting it back to `nvram.nvm`

6. Put the modified `nvram.nvm` into the UBIFS and package it as an image

7. In CFE's miniweb, select and upload the `misc1` image, then the factory nvram modification is completed (of course, you can also replace the `mtd10` partition with the `dd` command)

## Epilogue


This is not a detailed guide, but provides an accurate way. stop use the old idea of the old platform to guess how to modify the factory nvram of the new platform. 

To be clear this is not intended to modify the CFE, because the CFE of old platforms (such as RT-AC68U) includes the factory nvram variables in plaintext, so some people always refer to modifying the factory nvram variable as "modifying the CFE". let me make it clear, CFE is a bootloader, not text data. the CFE partition of Broadcom's new architecture does not contain factory nvram, but this greatly reduces the risk of flashing CFE, no one needs to flash the CFE just to modify variables.


**-- END --**


# Translator's Notes

This guide is originally in Chinese, under the [CC BY-NC-SA 4.0 license](https://creativecommons.org/licenses/by-nc-sa/4.0/), translated into English by GitHub user @JackMerlin and under the same license. According to the CC license requirements, it is necessary to point out the modifications made, and the modifications are almost all content, because this is a translation, of course, it contains many contents that the translator cannot understand, so I do not guarantee the accuracy of the translation, it is for reference only.

Original guide: https://www.bigxd.com/2021/04/976.html 

Translator: https://github.com/JackMerlin

March 15, 2022

## Languages

[Chinese 中文](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/blob/main/README-zh_CN.md)  |  [English](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/blob/main/README.md)  |  [Add more](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/new/main)

## What is HND platform?

> The bhnd driver provides a unified kernel bus interface to the on-chip interconnects used in Broadcom Home Networking Division (HND) devices.
>
> The Broadcom HND device family consists of SoCs (System On a Chip) and host-connected chipsets based on a common library of Broadcom IP cores connected via an internal hardware bus architecture. Drivers for these common IP cores are implemented against the unified bhnd interface. 

https://wiki.freebsd.org/dev/bhnd%284%29

In short HND is Broadcom's current platform, and Asuswrt-Merlin author Eric explains it:

> It's the name of the Broadcom platform used by their newer models starting with the RT-AC86U. Typically means the CPU is a BCM490x or a BCM675x.

http://www.snbforums.com/threads/is-the-rt-ac68u-v4-hnd.75436/post-721169

## Which routers are included in HND?

I can't enumerate all of them, but routers after RT-AC86U and above or equal to 1.5 GHz frequency are almost all HND. I can list some HND routers that support Asuswrt-Merlin custom firmware: RT-AC86U, GT-AC2900, RT-AX68U, RT-AX86U, RT-AX88U, GT-AX11000 and GT-AXE11000. It should be pointed out that RT-AX56U and RT-AX58U are very special, they are also called HND by Broadcom, but are still the old ARMv7 architecture, so I am not sure if this guide applies to them.

## Can variables be modified to adjust region or radio power?

Theoretically yes, but I won't discuss it here because it's probably illegal.

## Can I overclock my router?

Maybe, maybe not, I don't know, I'm just translating this guide and I haven't tried it yet when the translation is done.

## Will this brick my router?

Yes it is possible, since this partition is usually only available for manufacturer write, we don't fully understand how it works, and this article is pretty much the only one on the entire internet that explains what it does, so be prepared to get a bricked router.

**Anyway, you can edit this repository, improve translations, submit new translation languages and submit your experiences to help more people, If there is anything to improve here, please submit a PR directly! I love it!**


# License

[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

[![CC BY-NC-SA 4.0](https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

