
# Build ox64 Image with Buildroot

From https://pine64.org/documentation/Ox64/Software/Building/

```bash
mkdir -p ~/ox64
cd ~/ox64
git clone https://github.com/thommyho/buildroot
git clone https://github.com/thommyho/buildroot_bouffalo
```
Define an environment variable for the Buildroot Bouffalo overlay path:

```bash
cd ~/ox64
export BR_BOUFFALO_OVERLAY_PATH=$(pwd)/buildroot_bouffalo
```
change directory into the cloned Buildroot folder:
```bash
cd ~/ox64/buildroot
```
Apply the default configuration for Pine64 Ox64:
```bash
make BR2_EXTERNAL=$BR_BOUFFALO_OVERLAY_PATH pine64_ox64_defconfig
# Generates the default configuration for Pine64 Ox64 (~/ox64/buildroot/.config)
```
Then, you can run the menuconfig to customize your build:
```bash
make menuconfig
```

Within `menuconfig`, configure the following:+
- Select `Target Options`
  - Enable `Integer Multiplication and Division (M)`
  - Enable `Atomic Instructions (A)` using space key
  - Enable `Single-precision Floating-point (F)`
  - Enable `Double-precision Floating-point (D)`
  - Select `Target ABI`, set it to `lp64d` and press Exit
  - Select `Toolchain`, enable `Fortran support`, enable `OpenMP support`, and Save & Exit

Initiate the build process, but first make sure that your PATH variable contains no spaces.

## Wife Support
Modify the `~/ox64/1_buildroot_bouffalo/board/pine64/ox64/rootfs-overlay/etc/init.d/S41blctl` with your Wifi settings.

```bash
#!/bin/bash

# Connect to the specified Wi-Fi network
blctl connect_ap "YOUR-WIFI-SSID" "YOUR-WIFI-PASSWORD"

# Wait until the connection is established
sleep 4s

# Configure IP address using udhcpc
udhcpc -i bleth0
```

## Build the Image

Buildroot will output the needed files to the ~/ox64/buildroot/output/images directory in about 1 hour, according to your computer processing resources and internet connection speed.

```bash
make -j $(nproc)
```


---


Buildroot is a simple, efficient and easy-to-use tool to generate embedded
Linux systems through cross-compilation.

The documentation can be found in docs/manual. You can generate a text
document with 'make manual-text' and read output/docs/manual/manual.text.
Online documentation can be found at http://buildroot.org/docs.html

To build and use the buildroot stuff, do the following:

1) run 'make menuconfig'
2) select the target architecture and the packages you wish to compile
3) run 'make'
4) wait while it compiles
5) find the kernel, bootloader, root filesystem, etc. in output/images

You do not need to be root to build or run buildroot.  Have fun!
