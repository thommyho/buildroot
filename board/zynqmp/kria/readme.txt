**************************************************
Xilinx Kria SOM Starter Kits - ZynqMP SoC
**************************************************

This document describes the Buildroot support for the Kria KD240,
KR260 and KV260 starter kits by Xilinx, based on Kria SOM including the
Zynq UltraScale+ MPSoC (aka ZynqMP).  It has been tested with 
the KD240, KR260 and KV260 production boards.

Evaluation board features can be found here with the links below.

KD240:
https://www.xilinx.com/products/som/kria/kd240-drives-starter-kit.html

KR260:
https://www.xilinx.com/products/som/kria/kr260-robotics-starter-kit.html

KV260:
https://www.xilinx.com/products/som/kria/kv260-vision-starter-kit.html

How to build it
===============

Configure Buildroot: (use the command for the specific board)

    $ make zynqmp_kria_kd240_defconfig
    $ make zynqmp_kria_kr260_defconfig
    $ make zynqmp_kria_kv260_defconfig

Compile everything and build the rootfs image:

    $ make

Result of the build
-------------------

After building, you should get a tree like this:

    output/images/
    +-- atf-uboot.ub
    +-- bl31.bin
    +-- boot.bin
    +-- boot.vfat
    +-- Image
    +-- qspi.bin
    +-- rootfs.ext2
    +-- rootfs.ext4 -> rootfs.ext2
    +-- sdcard.img
    +-- system.dtb -> smk-k26-revA-sck-kv-g-revB.dtb
    +-- u-boot.itb
    `-- smk-k26-revA-sck-kv-g-revB.dtb

How to write the SD card
========================

WARNING! This will destroy all the card content. Use with care!

The sdcard.img file is a complete bootable image ready to be written
on the boot medium. To install it, simply copy the image to an SD
card:

    # dd if=output/images/sdcard.img of=/dev/sdX

Where 'sdX' is the device node of the SD.

Eject the SD card, insert it in the board, and power it up.

How to write boot.bin and u-boot.itb to QSPI boot flash
=======================================================

The Kria SOMs are preconfigured to boot initially from QSPI.
This makes these boards different from other ZynqMP boards
in that the boot.bin and u-boot.itb files need to be flashed
into the QSPI boot flash such that U-Boot can then load all
of the remaining images from the SD card.

In addition, the Kria Starter Kits QSPI comes pre-flashed with
a utility designed to make updating the QSPI flash memory
easier.

Instructions for using these utilities to update the files
in QSPI flash can be found on the wiki link below.

Please note that since the BIRT utility requires a single file
for flashing, the qspi.bin should be used as either ImageA or
ImageB.  The qspi.bin is a unified binary containing both the
boot.bin and u-boot.itb files in a single binary.

https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/1641152513/Kria+SOMs+Starter+Kits#Boot-FW-Update-Process

Additionally, it is possible to use u-boot for updating the
QSPI with new qspi.bin image with the u-boot commands below.

KV260 Flashing Instructions:
    $ sf probe
    $ fatload mmc 1 0x1000000 qspi.bin
    $ sf erase 0x200000 +$filesize
    $ sf write 0x1000000 0x200000 $filesize

KD240 / KR260 Flashing Instructions:
    $ usb start
    $ sf probe
    $ fatload usb 0 0x1000000 qspi.bin
    $ sf erase 0x200000 +$filesize
    $ sf write 0x1000000 0x200000 $filesize

It is possible to boot the Buildroot generated SD card image without
updating the QSPI qspi.bin image, so this is an optional step.
