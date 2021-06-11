![thunderbolt mac pro 5,1 cover](./images/thunderbolt-mac-pro-3.1.png)

[Home](./README.md) ▸ Thunderbolt Mac Pro 2008 Hackintosh with Gigabyte GC-Titan Ridge Card


# [Thunderbolt Mac Pro 3.1 2008 Hackintosh with Gigabyte GC-Titan Ridge Card]

This is my story to add Thunderbolt support to Hackintosh (Gigabyte Z390M Gaming) Mac Pro 2008 (3,1) with a Gigabyte GC-Titan Ridge Card. Before we begin a quick disclaimer below.

**Difficulty Level:** Medium

> **Disclaimer:** Try this at your own risk. I do not guaranty that this will work for you but I hope it does. This is purely for educational purposes and I am not responsible for any void in warranty. This is not a end all be all guide for getting the GC-Titan Ridge thunderbolt card working for every use case. These steps have worked for me but there might be additional steps that might be required for getting other thunderbolt devices to work. Please follow the links mentioned in the the `Useful Links` section below for more update to date information about the community postings on this topic.


## About my Mac Pro 3,1 (Hackintosh)

My mac pro has the following configurations:

- Processor:        3.60 Ghz ~ 5.00 Ghz 8-Core Intel i9 (9900K)
- Motherboard:      Gigabyte Z390M Gaming
- Memory:           64 GB 3200 Mhz DDR4
- Graphics          AMD Radeon RX Vega 56 8 GB

My purchase and upgrade story can be found [here](https://ameyrupji.github.io/macpro-5-1/) ([Github View](https://github.com/ameyrupji/macpro-5-1)).

## Prerequisites

- Obviously a Mac Pro and a Gigabyte GC-Titan Ridge Card [Amazon](https://amzn.to/2Aadohl)
- EEPROM Flash Bios USB Programmer [Amazon](https://amzn.to/37kgwTJ)
- Custom firmware for the Gigabyte GC-Titan Ridge Card v2.0 [Download](https://forums.macrumors.com/threads/testing-tb3-aic-with-mp-5-1.2143042/post-28291766). Links to old fimware can be found [here](./GC-TitanRidge-CustomFirmware.md)).


Device / Model	|Thunderbolt Firmware File Name	|Comment
------------ | ------------- | -------------
Gigabyte GC-Titan Ridge v1.0 add-in-card	| GC-TITAN-RIDGE-NVM23-Elias64Fr.bin TitanRidgeMacOSFirmware.bin	| PCIe card V1.0 (before Jul 2020) Big Sur 11.3 onwards requires TitanRidgeMacOSFirmware.bin, but USB 2.x and 3.x devices will not connect
Gigabyte GC-Titan Ridge v2.0 add-in-card |	GC-Titan-Ridge-V2.0-Mod-NVM50-CaseySJ.bin	| PCIe card V2.0 (since Jul 2020)

## My Upgrade Journey

### Gigabyte GC-Titan Ridge Card package contents

Here is a information about the package contents of Gigabyte GC-Titan Ridge Card [Link](https://www.gigabyte.com/us/Motherboard/GC-TITAN-RIDGE-rev-10#kf).

![image-gc-titan-ridge](./images/image-gc-titan-ridge.jpg)
**Source:** Gigabyte Website [Link](https://www.gigabyte.com/us/Motherboard/GC-TITAN-RIDGE-rev-10#kf)

### Disassemble GC-Titan Ridge

Once you unbox your titan ridge card it should look like this:

![image-gc-titanridge](./images/image-gc-titanridge.png)

Unscrew the 4 philips head screws to expose the PCB:

![image-gc-titan-ridge-disassembled](./images/image-gc-titan-ridge-disassembled.png):

The blue and the green chips that need to be flashed have a small dot with their respective colors.


### Download and Install flashrom using brew

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
brew install flashrom
```

![terminal-install-frashrom](./images/terminal-install-flashrom.png)


### Assembling Programmer

Look at the directions on the numbers on the green small board while assembling your programmer.

![image-assembling-programmer](./images/image-assembling-programmer.png)

### Connect to the blue chip 

Align the programmer as shown in the image below focus on one side of the clip first. Align one side and then the other side pushing down firmly.

![image-connected-blue-chip](./images/image-connected-blue-chip.png)

### Verify flashrom can detect your CH341A 24 25 Series EEPROM Flash Bios USB Programmer

```
flashrom -p ch341a_spi
```

![terminal-verify-flashrom-detection](./images/terminal-verify-flashrom-detection.png)


### Backup your Blue Chip Firmware

```
flashrom -p ch341a_spi -r OriginalFirmware-BlueChip.bin
```

![terminal-backup-bluechip-firmware](./images/terminal-backup-bluechip-firmware.png)

### Connect to green chip

Align the programmer as shown in the image below focus on one side of the clip first. Align one side and then the other side pushing down firmly.

![image-connected-green-chip](./images/image-connected-green-chip.png)

### Backup green chip firmware

```
flashrom -p ch341a_spi -r OriginalFirmware-GreenChip.bin
```
![terminal-backup-greenchip-firmware](./images/terminal-backup-greenchip-firmware.png)

### Connect to the blue chip and flashing TitanRidgeMacOSFirmware.bin custom firmware

Connect to blue chip again to be able to flash the firmware.

![image-connected-blue-chip](./images/image-connected-blue-chip.png)

```
flashrom -p ch341a_spi -w TitanRidgeMacOSFirmware.bin
```
![terminal-flashing-new-firmware](./images/terminal-flashing-new-firmware.png)

### Optional: Verify TitanRidgeMacOSFirmware.bin firmware is applied correctly

```
flashrom -p ch341a_spi -v TitanRidgeMacOSFirmware.bin
```

![terminal-verify-flash](./images/terminal-verify-flash.png)

Alternate verify - Check against original blue chip firmware should fail:

```
flashrom -p ch341a_spi -v OriginalFirmware-BlueChip.bin
```

### Connect pin 3 and pin 5 of jumper for Hot Swap capability

**Update:** March 14th, 2020

Lets now try connecting pin 3 and pin 5 of the THB_C Header Cable provided. Some folks have reported that without this their Mac freezes on boot. Have not noticed this issue on my end. [Reference Link](https://forums.macrumors.com/threads/testing-tb3-aic-with-mp-5-1.2143042/post-28261162).

![image-jumper-wire](./images/image-jumper-wire.png)

When connected the jumper from pin 3 to pin 5 should look like this.

![image-jumper-pin3-pin5](./images/image-jumper-pin3-pin5.png)

Connect this other end of the THB_C Header to the card into the J1 Header port of the card.

Alternate approaches suggested by community:

![image-jumper-pin3-pin5-alternate-1](./images/image-jumper-pin3-pin5-alternate-1.jpg)
![image-jumper-pin3-pin5-alternate-2](./images/image-jumper-pin3-pin5-alternate-2.png)
### Connect the card into the system

I connected this card in Slot 4 of my PCIE on Mac Pro. 

I also connected the display port outputs on my RX580 to the Mini Display input on the card (I did this as I want to use GC-Titan Rige card to power my Thunderbolt Displays).

![image-gc-titanridge-macpro-slot4](./images/image-gc-titanridge-macpro-slot4.png)

Inner view:

![image-gc-titanridge-macpro-slot4-inside](./images/image-gc-titanridge-macpro-slot4-inside.png)


### Thunderbolt devices tested

- SSD NVMe M.2 Thunderbolt 3 - TCM2T3-G40 [link](https://pt.aliexpress.com/item/4001215831558.html?spm=a2g0s.9042311.0.0.4d43b90a5zAoN1)

### USB devices tested

- External SSD (mSata) [Link](https://amzn.to/37BVy2Z) connected via USB-C cable [Link](https://amzn.to/37AzSEr) 

BlackMagic Speed Test with a mSata SSD. This test was done while on of the USB-C port was connected to 2 Apple Thunderbolt displays.

![blackmagic-speedtest-msata-ssd](./images/blackmagic-speedtest-msata-ssd.png)



### OS tested 

I have tested this is working on the following Operating Systems:

- Mojave 10.14.6 
- Catalina 10.15.3 
- Big Sur 11.4


### Known Issues

Issues that I have notices so far:

- ~~Hot swap devices works till at-least one device is connected to the GC-TITAN RIDGE card. I have only tried this with my thunderbolt displays if I connect one display to the card and leave it connected to the the card I can disconnect the second from the card or daisy chain and works after reconnecting. But if I remove both and try to reconnect then none of them work.~~ Solved by Connect pin 3 and pin 5 of jumper refer this section above.
- ~~I am using A pair of Apple Thunderbolt Display and have notices that the brightness functionality and USB ports behind the Thunderbolt display do not work.~~ Restart your system after a cold boot to enable USB and FaceTime HD Cameras on the Apple Thunderbolt displays. 

## Useful Links

- https://create.pro/titan-ridge-macos-firmware-release-download-now/?fbclid=IwAR2JecJfqwVpN3RNUsjvJce6pWDZJQQhOh-HbGptPRnJcPB-k9cgjIaIUhk
- https://linux.die.net/man/8/flashrom
- https://themacadmin.com/2012/02/15/mounting-the-efi-boot-partition-on-mac-os-x/
- https://www.macworld.co.uk/how-to/mac-software/mac-recovery-mode-3674052/
- https://forums.macrumors.com/threads/testing-tb3-aic-with-mp-5-1.2143042/page-27?post=28226897&fbclid=IwAR35B8htzngqkwVeMRcYC8FnC4dgu5NaaV777pmAdlQY-vxw-OQWLcUXydU#post-28226897
- https://forums.macrumors.com/threads/opencore-on-the-mac-pro.2207814/
- https://ss64.com/osx/bless.html
- https://forums.macrumors.com/threads/testing-tb3-aic-with-mp-5-1.2143042/post-28261032


## References to this Doumentation

**Writeups**

- https://www.pro-tools-expert.com/production-expert-1/2020/2/28/thunderbolt-3-on-mac-pro-51-cheese-grater-one-step-closer
- https://forums.macrumors.com/threads/testing-tb3-aic-with-mp-5-1.2143042/post-28293660
- https://forums.macrumors.com/threads/can-someone-please-make-a-titan-ridge-how-to-guide.2226638/post-28293877
- https://www.tonymacx86.com/threads/success-gigabyte-designare-z390-thunderbolt-3-i7-9700k-amd-rx-580.267551/page-2175
- https://www.tonymacx86.com/threads/asus-x299-catalina-support.279556/post-2126072
- https://uadforum.com/apollo-arrow-interfaces/44353-mac-pro-5-1-2012-universal-audio-apollo-twin-thunderbolt-2-a.html#post340850
- http://blog.greggant.com/posts/2018/05/07/definitive-mac-pro-upgrade-guide.html#thunderbolt
- https://forum.amd-osx.com/index.php?threads/ssdt-for-flashed-gc-titan-ridge.151/post-1934

**Videos**

 - ULTIMATE Mac Pro Thunderbolt upgrade! (By [PRESS RESET](https://www.youtube.com/channel/UCYfOQDGx24ZWrE-EUv_jSdw)) [YouTube](https://youtu.be/V9PtIa81FnI)
 - Thunderbolt 3 on Classic Mac Pro flashing GC-Titanridge full tutorial (By [OFXCPRODUCTIONS](https://www.youtube.com/channel/UCKf9SgUZVbqmd1fQDpI4njw)) [YouTube](https://youtu.be/R6jrc3TxQDc)
