# bcmrpi3-kernel-bis
Automated build of a tweaked version of the latest 64-bit `bcmrpi3_defconfig` Linux kernel for the RPi3B/B+, updated weekly.

## Description

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/raspberrypi/pi3/Raspberry_Pi_3_B_and_B_plus.jpg" alt="Raspberry Pi 3 B and B+" width="250px" align="right"/>

This project contains a weekly autobuild of the default branch (currently, `rpi-4.14.y`) of the [official Raspberry Pi Linux source tree](https://github.com/raspberrypi/linux), for the [64-bit Raspberry Pi 3 Model B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) and [B+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/).

As with its sister project [bcmrpi3-kernel](https://github.com/sakaki-/bcmrpi3-kernel), the baseline build configuration is the upstream `bcmrpi3_defconfig`, wherein the first 12 hex digits of the tip commit SHA1 hash are appended to `CONFIGLOCALVERSION` (with a separating hyphen). However, in *this* project, `-bis` is additionally appended to `CONFIGLOCALVERSION`, and (more importantly) additional tweaks are *also* applied to the kernel configuration before building, by running the [`conform_config.sh`](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/conform_config.sh) script.

> If you have changes you'd like to apply to the kernel config used by this project, please submit a PR targeting the [`conform_config.sh`](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/conform_config.sh) script. Changes should target the *end* of the script. Only edits which use the bundled convenience functions `set_kernel_config` and (rarely) `unset_kernel_config` will be considered for merging. Modularization is prefered wherever possible. Please include a short comment describing the changes, ideally including a link or bug ID.

A new build tarball is automatically created and uploaded as a release asset each week (unless the tip of the default branch is unchanged from the prior week, or an error occurs during the build process). The versions of the builds in this project will always mirror those of [bcmrpi3-kernel](https://github.com/sakaki-/bcmrpi3-kernel), commencing 1 June 2018.

> The default branch is used, as that is generally given most attention for e.g. VC4 backports.

As an (historical) example, on 1 June 2018, the default branch was `rpi-4.14.y`, and the latest commit was `4fca48b7612da3ff5737e27da15b0964bdf4928f` (the short form of which is `4fca48b7612d`). The created release was [4.14.44.20180601](https://github.com/sakaki-/bcmrpi3-kernel-bis/releases/4.14.44.20180601), within which the kernel tarball was `bcmrpi3-kernel-bis-4.14.44.20180601.tar.xz`, and the corresponding kernel release name was `4.14.44-v8-4fca48b7612d-bis+`.

Each kernel release tarball currently provides the following files:
* `/boot/kernel8.img` (this is the bootable 64-bit kernel);
* `/boot/bcm-2710-rpi-3-b.dtb`, `/boot/bcm-2710-rpi-3-b-plus.dtb` and `/boot/bcm-2837-rpi-3-b.dtb` (the device tree blobs);
* `/lib/modules/<kernel release name>/...` (the module set for the kernel);

The current kernel tarball may be downloaded from the link below (or via `wget`, or via the corresponding `bcmrpi3-kernel-bin` ebuild, per the [instructions following](#installation)):

Variant | Version | Most Recent Image
:--- | ---: | ---:
Kernel, dtbs and modules | 4.14.91.20190108 | [bcmrpi3-kernel-bis-4.14.91.20190108.tar.xz](https://github.com/sakaki-/bcmrpi3-kernel-bis/releases/download/4.14.91.20190108/bcmrpi3-kernel-bis-4.14.91.20190108.tar.xz)

The corresponding kernel configuration (derived via `make bcmrpi3_defconfig && conform_config.sh && make olddefconfig`) may be viewed [here](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/config). The 'baseline' `bcmrpi3_defconfig` may be viewed [here](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/bcmrpi3_config), the `conform_config.sh` script may be viewed [here](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/conform_config.sh), and a diff between the 'tweaked' and 'baseline' configurations may be viewed [here](https://github.com/sakaki-/bcmrpi3-kernel-bis/blob/master/vs_bcmrpi3_config.diff).

> A list of all releases may be seen [here](https://github.com/sakaki-/bcmrpi3-kernel-bis/releases).

## <a name="installation"></a>Installation

To deploy (assuming that your RPi3's micro SD-card's first partition is mounted as `/boot`, and you are already running a 64-bit RPi3 image, such as my [gentoo-on-rpi3-64bit](https://github.com/sakaki-/gentoo-on-rpi3-64bit)) simply download, untar into the root directory, and reboot:
```console
pi64 ~ # cp /boot/kernel8.img{,.old}
pi64 ~ # wget -c https://github.com/sakaki-/bcmrpi3-kernel-bis/releases/download/4.14.91.20190108/bcmrpi3-kernel-bis-4.14.91.20190108.tar.xz
pi64 ~ # tar -xJf bcmrpi3-kernel-bis-4.14.91.20190108.tar.xz -C /
pi64 ~ # sync && reboot
```

Alternatively, if you have my [rpi3 overlay](https://github.com/sakaki-/rpi3-overlay) installed (it is pre-installed on the [gentoo-on-rpi3-64bit](https://github.com/sakaki-/gentoo-on-rpi3-64bit) image), you can simply emerge the `bcmrpi3-kernel-bis-bin` package (a new ebuild is automatically created to mirror each release here). For example, to install the latest available version (and start using it):
```console
pi64 ~ # emaint sync --repo rpi3
pi64 ~ # emerge -av bcmrpi3-kernel-bis-bin
pi64 ~ # reboot
```

Or, to install a particular version (e.g.):
```console
pi64 ~ # emaint sync --repo rpi3
pi64 ~ # emerge -av =bcmrpi3-kernel-bis-bin-4.14.91.20190108
pi64 ~ # reboot
```

> NB: these prebuilt kernels and ebuilds are provided as a convenience only. Use at your own risk! **Given that the releases in this project are created automatically, and particularly since they include user-submitted tweaks to the 'official' `bcmrpi3_defconfig`, there is no guarantee that any given kernel will boot correctly.** A 64-bit kernel is necessary, but not sufficient, to boot the RPi3 in 64-bit mode; you also need the supporting firmware, configuration files, and userland software (see for example my [gentoo-on-rpi3-64bit](https://github.com/sakaki-/gentoo-on-rpi3-64bit) project, or NeddySeagoon's [Raspberry Pi 3 64 bit Install](https://wiki.gentoo.org/wiki/Raspberry_Pi_3_64_bit_Install) page on the Gentoo wiki, for more information).
