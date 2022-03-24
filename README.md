# gentoo-on-rpi-64bit
Bootable 64-bit Gentoo image for the Raspberry Pi 4 Model B, Model Pi 3 B and B+, and Model Pi Zero 2 W, with Linux 5.4, OpenRC, Xfce4, VC4/V3D, camera & h/w codec support, profile 17.0, weekly-autobuild binhost

30 Oct 2020: sadly, due legal obligations arising from a recent change in Sakaki's 'real world' job, she stepped down from all maintenence roles related to Gentoo, leaving all her projects in limbo.  

This is the community maintained continuation of her work.  Want to get involved?  Open an issue here, or join the Discord server at https://discord.gg/a9VqTGHPy6 

## Description

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/raspberrypi/pi4/Raspberry_Pi_3_B_and_B_plus_and_4_B.jpg" alt="[Raspberry Pi 4B, 3B and B+]" width="250px" align="right"/>

This project is a bootable, microSD card **64-bit Gentoo image for the [Raspberry Pi 4 model B](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/), 
[3 model B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/),  [3 model B+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/),
[Pi Zero 2 W](https://www.raspberrypi.com/products/raspberry-pi-zero-2-w/)** single board computers (SBC).

The image's userland contains a complete (OpenRC-based) Gentoo system (including a full Portage tree) - so you can run `emerge` operations immediately - and has been pre-populated with a reasonable package set (Xfce, LibreOffice, VLC, Kodi, GIMP, etc.) so that you can get productive *without* having to compile anything first! Unless you *want* to, of course; this being Gentoo, GCC, Clang, IcedTea (OpenJDK 8), Go, Rust and various versions of Python are of course bundled also ^-^ As of version 1.2.0 of the image, all userland software has been built under Gentoo's 17.0 profile, and, as of version 1.5.0 of the image, **the new RPi4 Model B is also supported** (and to reflect this, the project itself has been renamed, from `gentoo-on-rpi3-64bit` to `gentoo-on-rpi-64bit` ^-^).

The kernel and userland are both 64-bit (`arm64`/`aarch64`), and support for the Pi's [VC4](https://wiki.gentoo.org/wiki/Raspberry_Pi_VC4) GPU (VC6 on the Pi4) has been included (using [`vc4-fkms-v3d`](https://www.raspberrypi.org/forums/viewtopic.php?f=29&t=159853) / Mesa), so rendering performance is reasonable (e.g., `glxgears` between 400 and 1200fps, depending on load and system type; real-time video playback). The Pi's onboard Ethernet, WiFi (dual-band on the RPi3 B+ / Pi4 B) and Bluetooth adaptors are supported, as is the official 7" [touchscreen](#touchscreen) (if you have one). Sound works too, both via HDMI (given an appropriate display), and the onboard headphone jack. <img src="https://user-images.githubusercontent.com/227313/159844737-7bf2374b-c669-4c67-8a2a-d1844c3adde1.jpg" alt="[Raspberry Pi Zero 2 W]" width="250px" align="right"/>
As of version 1.1.0 of the image, a [weekly-autobuild binhost](#binhost), custom [Gentoo profile](#profile), and [binary kernel package](#binary_kp) have been provided, making it relatively painless to keep your system up-to-date (and, because of this, [`genup`](https://github.com/sakaki-/genup) has been configured to run [automatically once per week](#weekly_update), by default). As of version 1.4.0 of the image, access to the RPi3's hardware video codecs (and camera module, if you have one) [is supported](#v4l2) too, via the V4L2 framework, and, as of version 1.5.0, these features are available on the RPi4 also. Additionally, on the RPi4, the use of dual monitors is supported (but not required) as of version 1.5.0 (as is accelerated graphics, via V3D / Mesa). And, there is no 3GiB 'memory ceiling' anymore: if you are fortunate enough to own a 8GiB Pi4, all 8GiB of that RAM is usable. As of 1.5.2, [64-bit MMAL userland](#mmal) is supported (and used by bundled tools like `raspivid`), as is automated update of the RPi4's onboard EEPROM firmware. And finally, as of 1.6.0, `rpi-5.4.y` kernels are supported, the FOSS Jitsi videoconferencing server is bundled, and `elogind` is used in place of `consolekit`.


Here's a screenshot of the image running on a dual-display RPi4 B (click to show a higher resolution view):

<a href="https://raw.githubusercontent.com/sakaki-/resources/master/raspberrypi/pi4/demo-screenshot-1.6.0-pi4-ds-white-bg-v2.jpg"><img src="https://raw.githubusercontent.com/sakaki-/resources/master/raspberrypi/pi4/demo-screenshot-1.6.0-pi4-ds-white-bg-small-v2.jpg" alt="[gentoo-on-rpi-64bit in use on Pi4 (screenshot)]" width="960px"/></a>

The image may be downloaded from the link below (or via `wget`, per the instructions which follow).

<a id="downloadlinks"></a>Variant | Version | Image
:--- | ---: | ---: | 
Raspberry Pi  4B, 3B/B+ 64-bit Full | alpha9 | [genpi64-arm64-openrc-desktop-alpha9.img.zst](https://s3.genpi64.com/images/genpi64-arm64-openrc-desktop-alpha9.img.zst)
Raspberry Pi 4B, 3B/B+ 64-bit Lite | alpha9 | [genpi64-arm64-openrc-lite-alpha9.img.zst](https://s3.genpi64.com/images/genpi64-arm64-openrc-lite-alpha9.img.zst)
Raspberry Pi 4B, 3B/B+ 64-bit Lite (systemd) | alpha9 | [genpi64-arm64-systemd-lite-alpha9.img.zst](https://s3.genpi64.com/images/genpi64-arm64-systemd-lite-alpha9.img.zst)

**NB:** most users will want the first, full image ([genpi64desktop-latest.img.zst](https://s3.genpi64.com/images/genpi64-arm64-openrc-desktop-alpha9.img.zst)) - the 'lite' variant ([genpi64-arm64-openrc-lite-alpha9.img.zst](https://s3.genpi64.com/images/genpi64-arm64-openrc-lite-alpha9.img.zst)) boots to a command-line (rather than a graphical desktop), and is intended only for experienced Gentoo users (who wish to to *e.g.* set up a server).

Please read the instructions below before proceeding. Also please note that all images (and binary packages) are provided 'as is' and without warranty. You should also be comfortable with the (at the moment, unavoidable) non-free licenses required by the firmware and boot software supplied on the image before proceeding: these may be reviewed [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/tree/master/licenses).

> It is sensible to install Gentoo to a **separate** microSD card from that used by your default Raspbian system; that way, when you are finished using Gentoo, you can simply power off, swap back to your old card, reboot, and your original system will be just as it was.

> Please also note that support for `arm64` is still in its [early stages](https://wiki.gentoo.org/wiki/Raspberry_Pi) with Gentoo, so it is quite possible that you may encounter strange bugs etc. when running a 64-bit image such as this one. A lot of packages [have been `* ~*` keyworded](https://github.com/sakaki-/genpi64-overlay/tree/master/profiles/targets/rpi3/package.accept_keywords) to get the system provided here to build... but hey, if you like Gentoo, little things like that aren't likely to put you off ^-^

## Table of Contents

- [Prerequisites](#prerequisites)
- [Downloading and Writing the Image](#downloading-and-writing-the-image)
  * [Full Image (genpi64.img.zst)](#regularimage)
  * [Lite Image (genpi64lite.img.zst)](#liteimage)
- [Booting!](#booting)
- [Using Gentoo](#using-gentoo)
- [Keeping Your System Up-To-Date](#keeping-your-system-up-to-date)
  * [Installing New Packages Under Gentoo](#installing-new-packages-under-gentoo)
- [Miscellaneous Configuration Notes, Hints, and Tips](#miscellaneous-configuration-notes-hints-and-tips-skip)
- [Maintenance Notes (Advanced Users Only)](#maintenance-notes-advanced-users-only-skip)
  * [Have your Gentoo PC Do the Heavy Lifting!](#have-your-gentoo-pc-do-the-heavy-lifting)
  * [Using your RPi3 as a Headless Server](#rpi3_headless)
- [Miscellaneous Points (Advanced Users Only)](#miscellaneous-points-advanced-users-only-skip)
  * [RPi-Specific Ebuilds](#rpi-specific-ebuilds)
  * [New Features to Expedite Regular System Updating](#new-features-to-expedite-regular-system-updating)
  * [Subscribed Ebuild Repositories (aka Overlays)](#subscribed-ebuild-repositories-aka-overlays)
- [Project Wiki](#projectwiki)
- [Help Wanted!](#help-wanted)
- [Acknowledgement](#acknowledgement)
- [Feedback Welcome!](#feedback-welcome)

## Prerequisites

To try this out, you will need:
* For the **full** image (which most users will want), a [microSD](https://en.wikipedia.org/wiki/Secure_Digital) card of _at least_ 6GB capacity (as this image is 1GiB compressed, 5.5GiB uncompressed, so it should fit on any card marked as >= 8GB). For the **'lite'** image, a card marked as >= 4GB should suffice (as this image is 755MiB compressed, 3.5GiB uncompressed). If you intend to build many additional large packages (or kernels) on your RPi, a card of >16GB is recommended (the root partition will [automatically be expanded](#morespace) to fill the available space on your microSD card, on first boot). Depending on the slots available on your PC, you may also need an adaptor to allow the microSD card to be plugged in (to write the image to it initially). [Class A1 cards](https://www.raspberrypi.org/forums/viewtopic.php?p=1517864#p1517864) are particularly recommended, but not required.
   > I have found most SanDisk cards work fine; if you are having trouble, a good sanity check is to try writing the [standard Raspbian 32-bit image](https://www.raspberrypi.org/downloads/raspbian/) to your card, to verify that your Pi4 (or Pi3) will boot with it, before proceeding.

* A Raspberry Pi 4 Model B, or Pi 3 Model B or B+ (obviously!).
For simplicity, I am going to assume that you will be logging into the image (at least initially) via an (HDMI) screen and (USB) keyboard connected directly to your Pi, rather than e.g. via `ssh` (although, for completeness, it *is* possible to `ssh` in by editing `startup.sh` with networking details, explained in the [Lite Image section](#liteimage), or via the Ethernet interface (which has a DHCP client running), if you can determine the allocated IP address, for example from your router, or via [`nmap`](https://security.stackexchange.com/a/36200)).
A [decent power supply](https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=138636) is recommended.

* A PC to decompress the image and write it to the microSD card. This is most easily done on a Linux machine of some sort, but it is straightforward on a Windows or Mac box too (for example, by using [Etcher](https://etcher.io), which is a nice, all-in-one cross-platform graphical tool for exactly this purpose - it's free and open-source; or, bzt's [usbimager](https://gitlab.com/bztsrc/usbimager), a very lightweight cross-platform open source image writer). In the instructions below I'm going to assume you're using Linux.
   > It is possible to use your Raspberry Pi for this task too, if you have an external card reader attached, or if you have your root on e.g. USB (and take care to unmount your existing /boot directory before removing the original microSD card), or are booted directly from USB. Most users will find it simplest to write the image on a PC, however.

## Downloading and Writing the Image

Choose either the full (recommended for most users) or 'lite' (command-line only) variant, then follow the appropriate instructions below ([full](#regularimage) or [lite](#liteimage)).

> If you are using a Windows or Mac box, or prefer to use a GUI tool in Linux, I recommend you download your preferred image via your web browser using the [links](#downloadlinks) above, and then check out either of the the free, open-source, cross-platform tools [Etcher](https://etcher.io) or [usbimager](https://gitlab.com/bztsrc/usbimager) to write it to microSD card. Then, once you've done that, continue reading at ["Booting!"](#booting) below.

> Alternatively, for those who prefer the Raspberry Pi [NOOBS](https://www.raspberrypi.org/documentation/installation/noobs.md) installer GUI, both images are now available for installation using [PINN](https://github.com/procount/pinn) (called `gentoo64` and `gentoo64lite` there). PINN is a fork of NOOBS and includes a number of additional advanced features. Please see [this post](https://forums.gentoo.org/viewtopic-p-8122236.html#8122236) for further details. *Many thanks to [procount](https://github.com/procount) for his work on this.*

### <a id="regularimage"></a>Full Image (`genpi64desktop-latest.img.zst`)

On your Linux box, issue (you may need to be `root`, or use `sudo`, for the following, hence the '#' prompt):
```console
# wget -c https://fi.packages.genpi64.com/genpi-aarch64-desktop-latest.img.zst
# zstd -d genpi64desktop-latest.img.zst -o genpi64desktop-latest.img
```



<img src="https://github.com/sakaki-/resources/raw/master/raspberrypi/pi4/rpi4-desktop.png" alt="RPi4 Running Full Image" height="200px" align="right"/>

to fetch the compressed disk image file (~1,961MiB).

Next, insert (into your Linux box) the microSD card on which you want to install the image, and determine its device path (this will be something like `/dev/sdb`, `/dev/sdc` etc. (if you have a [USB microSD card reader](http://linux-sunxi.org/Bootable_SD_card#Introduction)), or perhaps something like `/dev/mmcblk0` (if you have e.g. a PCI-based reader); in any case, the actual path will depend on your system - you can use the `lsblk` tool to help you). Unmount any existing partitions of the card that may have automounted (using `umount`). Then issue:

> **Warning** - this will *destroy* all existing data on the target drive, so please double-check that you have the path correct! As mentioned, it is wise to use a spare microSD card as your target, keeping your existing Raspbian microSD card in a safe place; that way, you can easily reboot back into your existing Raspbian system, simply by swapping back to your old card.

```console
# dd if=genpi64desktop-latest.img of=/dev/sdX bs=1M status=progress; sync
```
Alternatively, you can use 
```console
# zstdcat -d genpi64desktop-latest.img.zst -vvv --no-sparse -o /dev/sdX
```
to write the compressed file directly.  This doesn't require the 5GB of space on your local drive, and may be faster than using `dd`, but doesn't let you work with the image on your local computer *before* writing to the sd card.

Substitute the actual microSD card device path, for example `/dev/sdc`, for `/dev/sdX` in the above command. Make sure to reference the device, **not** a partition within it (so e.g., `/dev/sdc` and not `/dev/sdc1`; `/dev/sdd` and not `/dev/sdd1` etc.)
> If, on your system, the microSD card showed up with a path of form `/dev/mmcblk0` instead, then use this as the target, in place of `/dev/sdX`. For this naming format, the trailing digit *is* part of the drive name (partitions are labelled as e.g. `/dev/mmcblk0p1`, `/dev/mmcblk0p2` etc.). So, for example, you might need to use `zstdcat genpi64.img.zst --no-sparse -d -o /dev/mmcblk0 && sync`.

The above `zstdcat` to the microSD card will take some time, due to the decompression (it takes between 5 and 25 minutes on my machine, depending on the microSD card used). It should exit cleanly when done - if you get a message saying 'No space left on device', then your card is too small for the image, and you should try again with a larger capacity one.
> <a id="morespace"></a>Note that on first boot, the image will _automatically_ attempt to resize its root partition (which, in this image, includes `/home`) to fill all remaining free space on the microSD card, by running [this startup service](https://github.com/sakaki-/genpi64-overlay/blob/master/sys-apps/rpi3-init-scripts/files/init.d_autoexpand_root-4); if you _do not_ want this to happen (for example, because you wish to add extra partitions to the microSD card later yourself), then simply **rename** the (empty) sentinel file `autoexpand_root_partition` (in the top level directory of the `vfat` filesystem on the first partition of the microSD card) to `autoexpand_root_none`, before attempting to boot the image for the first time.

> **BTRFS Note** - the image uses btrfs, with significant compression to reduce the disk space used and file read time.  The btrfs UUID is set at filesystem creation time, and cannot be changed on a live system.  Only one device with any given btrfs UUID can be mounted at a time.  To prevent possible problems with multiple copies of the image in use at once, after writing the image via `dd` or `zstdcat`, you should change the btrfs UUID via

```console
# btrfstune -u /dev/sdX2
```

This will generate a new, random, uuid for the second partition.  In the future we want to do this on first boot, but it must be done *before* the root filesystem is mounted.

Now continue reading at ["Booting!"](#booting) below.

### <a id="liteimage"></a>Lite Image (`genpi64lite.img.xz`)

> Please note: this image boots to a command line, not a GUI, and is only suitable for experienced Gentoo users. If unsure, choose the [full image](#regularimage) above, instead.

On your Linux box, issue (you may need to be `root`, or use `sudo`, for the following, hence the '#' prompt):
```console
# wget -c https://s3.genpi64.com/images/genpi-arm64-lite-latest.img.zst
# zstd -d genpi64-arm64-lite-latest.img.zst -o genpi64-arm64-lite-latest.img
```

<img src="https://github.com/sakaki-/resources/raw/master/raspberrypi/pi4/rpi4-console.png" alt="[RPi4 Running Lite Image]" height="200px" align="right"/>

to fetch the compressed disk image file (~956MiB)


Next, insert (into your Linux box) the microSD card on which you want to install the image, and determine its device path (this will be something like `/dev/sdb`, `/dev/sdc` etc. (if you have a [USB microSD card reader](http://linux-sunxi.org/Bootable_SD_card#Introduction)), or perhaps something like `/dev/mmcblk0` (if you have e.g. a PCI-based reader); in any case, the actual path will depend on your system - you can use the `lsblk` tool to help you). Unmount any existing partitions of the card that may have automounted (using `umount`). Then issue:

> **Warning** - this will *destroy* all existing data on the target drive, so please double-check that you have the path correct! As mentioned, it is wise to use a spare microSD card as your target, keeping your existing Raspbian microSD card in a safe place; that way, you can easily reboot back into your existing Raspbian system, simply by swapping back to your old card.

```console
# dd if=genpi64-arm64-lite-latest.img of=/dev/sdX bs=1M status=progress; sync
```

Substitute the actual microSD card device path, for example `/dev/sdc`, for `/dev/sdX` in the above command. Make sure to reference the device, **not** a partition within it (so e.g., `/dev/sdc` and not `/dev/sdc1`; `/dev/sdd` and not `/dev/sdd1` etc.)
> If, on your system, the microSD card showed up with a path of form `/dev/mmcblk0` instead, then use this as the target, in place of `/dev/sdX`. For this naming format, the trailing digit *is* part of the drive name (partitions are labelled as e.g. `/dev/mmcblk0p1`, `/dev/mmcblk0p2` etc.). So, for example, you might need to use `xzcat genpi64lite.img.xz -d --no-sparse -o /dev/mmcblk0 && sync`.

The above `xzcat` to the microSD card will take some time, due to the decompression (it takes between 5 and 10 minutes on my machine, depending on the microSD card used). It should exit cleanly when done - if you get a message saying 'No space left on device', then your card is too small for the image, and you should try again with a larger capacity one.

Now continue reading at ["Booting!"](#booting), immediately below. Note, however, that in the case of this 'lite' image, after the startup / resize process has completed, you will be landed at a console login prompt (although it is simple enough to [parlay to a basic X11 desktop](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Setup-a-Simple-X11-Desktop-Environment-on-the-Lite-Image) if you wish). The login credentials are the same as for the full image, *viz.*:
* default user **demouser**, password **Raspberrypi64!**;
* **root** user password also **GenPi64@**.


## <a id="booting"></a>Booting!

Begin with your RPi4 (or RPi3) powered off. Remove the current (Raspbian or other) microSD card from the board (if fitted), and store it somewhere safe.

Next, insert the (Gentoo) microSD card you just wrote the image to into the Pi. Ensure you have peripherals connected (with the main display plugged into the **HDMI0** port - the one nearer the USB-C power connector - if using an RPi4), and apply power.

You should see the Pi's standard 'rainbow square' on-screen for about 2 seconds, then the display will go blank for about 10 seconds, and then (once the graphics driver has loaded) a text console will appear, showing OpenRC starting up. On this first boot (unless you have renamed the sentinel file `autoexpand_root_partition` in the microSD card's first partition to `autoexpand_root_none`), the system will, after a further 10 seconds or so, _automatically resize_ the root partition to fill all remaining free space on the drive, and, having done this, reboot (to [allow the kernel to see](http://unix.stackexchange.com/questions/196435/is-it-possible-to-enlarge-the-partition-without-rebooting) the new partition table). You should then see the 'rainbow square' startup sequence once more, and then the system will resize its root filesystem, to fill the newly enlarged partition. Once this is done, it will launch a standard Xfce desktop, logged in automatically to the pre-created `demouser` account. A simple configuration application will also be autostarted; this allows you to e.g. change screen resolution, if required:

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/raspberrypi/pi4/xfce-desktop-small.png" alt="[Baseline Xfce desktop]" width="960px"/>

> If your display looks OK, there's no need to make any changes immediately - just click <kbd>OK</kbd> to dismiss the welcome dialog, and then click on the <kbd>Cancel</kbd> button to close out the configuration tool itself.

The whole process (from first power on to graphical desktop) should take less than three minutes or so (on subsequent reboots, the resizing process will not run, so startup will be a _lot_ faster).

> The initial **root** password on the image is **GenPi64@**. The password for **demouser** is also **Raspberrypi64!** (you may need this if e.g. the screen lock comes on; you can also do `sudo su --login root` to get a root prompt at the terminal, without requiring a password). These passwords are set by the [`autoexpand-root`](https://github.com/sakaki-/genpi64-overlay/blob/master/sys-apps/rpi3-init-scripts/files/init.d_autoexpand_root-4) startup service. Note that the screensaver for `demouser` has been disabled by default on the image.

> NB - if your connected computer monitor or TV output appears **flickering or distorted**, you may need to change the settings in the file `config.txt`, located in the microSD card's first partition (this partition is formatted `vfat` so you should be able to edit it on any PC; alternatively, when booted into the image, it is available at `/boot/config.txt`). Any changes made take effect on the next restart. For an explanation of the various options available in `config.txt`, please see [these notes](https://www.raspberrypi.org/documentation/configuration/config-txt/README.md) (the [shipped defaults](https://github.com/sakaki-/genpi64-overlay/blob/master/sys-boot/rpi3-boot-config/files/config.txt-9) should work fine for most users, however). As of v1.3.1 of this image, you can also use the bundled GUI tool (shown in the screenshot above) to modify (some of) these settings. The program, `pyconfig_gen`, is, as noted, automatically started on first login, and is available under <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>RPi Config Tool</kbd>.

> If the edges of the desktop appear "clipped" by your display (this mostly happens when using HDMI TVs), issue `sudo mousepad /boot/config.txt`, comment out the line `disable_overscan=1` (so it reads instead `#disable_overscan=1`), save the file, and then reboot (from v1.3.1, you can also enable and configure overscan via the <kbd>Rpi Config Tool</kbd>, as noted above). Please note that with modern boot firmware, [overscan is ignored](https://github.com/raspberrypi/linux/issues/3059#issuecomment-510108535) if a DMT mode is set.

> Tip: if you'd like to set up a persistent dual-monitor setup on an RPi4, please see my short tutorial on this project's open wiki [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Dual-Displays-on-your-RPi4) (this also contains useful hints for setting up a single display).

## <a id="using_gentoo"></a>Using Gentoo

The supplied image contains a fully-configured `arm64` Gentoo system (*not* simply a [minimal install](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#Minimal_installation_CD) or [stage 3](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#What_are_stages_then.3F)), with a complete Portage tree already downloaded, so you can immediately perform `emerge` operations etc. Be aware that, as shipped, it uses **UK locale settings, keyboard mapping, timezone and WiFi regulatory domain**; however, these are easily changed if desired. See the Gentoo Handbook ([here](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Base#Timezone) and [here](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/System#Init_and_boot_configuration)) for details on the first three; to customize the WiFi regulatory domain, start the <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>RPi Config Tool</kbd> app, select the <kbd>WiFi</kbd> tab, choose your location from the drop-down, click <kbd>Save and Exit</kbd>, and reboot when prompted.

> Please note that for non-UK keyboards, **prior** to v1.5.4 of the image, you will also need to edit the file `/etc/X11/xorg.conf.d/00-keyboard.conf` and replace  `gb` in the line `Option "XkbLayout" "gb"` with the appropriate code from [this list](https://pastebin.com/v2vCPHjs). Leave the rest of the file as-is, save, then reboot for the change to take effect. You will need to be the `root` user, or use `sudo` to edit this file (e.g., type `sudo mousepad /etc/X11/xorg.conf.d/00-keyboard.conf` in a terminal). **From** v1.5.4 however, a simpler GUI-driven method is available. Simply click <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>Keyboard</kbd>, select the <kbd>Layout</kbd> tab, ensure <kbd>Use system defaults</kbd> is _unchecked_, and then, in the <kbd>Keyboard layout</kbd> section, click on <kbd>Add</kbd> to insert the layouts you need to use. For example, the image has `English (UK)` and `English (US)` selected, but you should obviously choose whatever works for you. Do not add more than four layouts (or the switcher plugin will not work correctly). You can use the arrow keys to re-order the list (if you have more than one layout); the one at the _top_ will be the default activated at login time. Once done, click on <kbd>Close</kbd> to dismiss the dialog. You should now find that you can switch layouts (if you have specified more than one) either by clicking on the top panel-bar keyboard plugin (the one next to the `Demo User` text on the far right) or by pressing the hotkey (by default, this is <kbd>Windows Key</kbd><kbd>Space</kbd>).

If you have an RPi4, and would like to configure a **dual monitor** setup, please see [these notes](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Dual-Displays-on-your-RPi4) (also contains some useful hints for single monitor deployment, applicable to all models).

If you wish to add Bluetooth peripherals (e.g., a wireless mouse and/or keyboard) to your RPi, please see [below](#add_bt_device).

If you would like to run **benchmarks** on your RPi system, please see [these notes](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Effective-Benchmarking-on-your-64-Bit-RPi-System).

For hardware-accelerated YouTube playback, please see [this post](https://www.raspberrypi.org/forums/viewtopic.php?p=1589280#p1589280). Also please note that as of v1.5.4 of the image, you can (particularly on a Pi4B with a fast Internet connection) use <kbd>Applications</kbd>&rarr;<kbd>Internet</kbd>&rarr;<kbd>SMTube (YouTube, Hi-Res)</kbd> to play back videos from YouTube in 1080p, where available; this is significantly better than the currently bundled browsers (Firefox and Chromium) can achieve.

The `@world` set of packages (from `/var/lib/portage/world`) pre-installed on the image may be viewed [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/world-packages) (note that the version numbers shown in this list are Gentoo ebuilds, but they generally map 1-to-1 onto upstream package versions).
> The *full* package list for the image (which includes @system and dependencies) may also be viewed, [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/world-all-packages).

The system on the image has been built via a minimal install system and stage 3 from Gentoo (`arm64`, available [here](http://distfiles.gentoo.org/experimental/arm64/)), but _all_ binaries (libraries and executables) have been rebuilt (with profile 17.0) to run optimally on the Raspberry Pi 4 B's Cortex-A72-based SoC, while still retaining backwards compatibility with the older Pi 3 (B and B) model's Cortex-A53. The `/etc/portage/make.conf` file used on the image may be viewed [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/make.conf), augmented by the [custom profile's](#profile) [`make.defaults`](https://github.com/sakaki-/genpi64-overlay/tree/master/profiles/targets/rpi3/make.defaults). The `CHOST` on the image is `aarch64-unknown-linux-gnu` (per [these notes](https://wiki.gentoo.org/wiki/User:NeddySeagoon/Pi3#Getting_Started)). All packages have been brought up to date against the Gentoo tree as of 11 June 2020. The image contains *two* kernels: one ([`bcmrpi3-kernel-bis`](https://github.com/sakaki-/bcmrpi3-kernel-bis)) is used when booting on the RPi3 B/B+, and the other ([`bcm2711-kernel-bis`](https://github.com/sakaki-/bcm2711-kernel-bis)) when booting on an RPi4. The correct kernel is chosen automatically at boot time. Each kernel has a distinct release name, and so a separate module set in `/lib/modules/`.

> Note: the `CFLAGS` used for the image build is `-march=armv8-a+crc -mtune=cortex-a72 -O2 -pipe`. You can of course re-build selective components with more aggressive flags yourself, should you choose. As the SIMD FPU features are standard in ARMv8, there is no need for `-mfpu=neon mfloat-abi=hard` etc., as you would have had on e.g. the 32-bit ARMv7a architecture. Note that AArch64 NEON also has a full IEEE 754-compliant mode (including handling denormalized numbers etc.), there is also no need for `-ffast-math` flag to fully exploit the FPU either (again, unlike earlier ARM systems). Please refer to the official [Programmer’s Guide for ARMv8-A](http://infocenter.arm.com/help/topic/com.arm.doc.den0024a/DEN0024A_v8_architecture_PG.pdf) for more details. Neither the Pi3 nor Pi4 is shipped with the ARMv8 hardware crypto extensions enabled, unfortunately.

When logged in as `demouser`, it is sensible to change the password (from `raspberrypi64`) and `root`'s password (also from `raspberrypi64`). Open a terminal window and do this now:
```console
demouser@pi64 ~ $ passwd
Changing password for demouser.
(current) UNIX password: <type raspberrypi64 and press Enter>
New password: <type your desired password, and press Enter>
Retype new password: <type the desired password again, and press Enter>
passwd: password updated successfully
demouser@pi64 ~ $ su -
Password: <type raspberrypi64 and press Enter, to become root>
pi64 ~ # passwd
New password: <type your desired password for root, and press Enter>
Retype new password: <type the desired root password again, and press Enter>
passwd: password updated successfully
pi64 ~ # exit
logout
demouser@pi64 ~ $
```

<a id="add_new_account"></a>If you want to create your own account, you can do so easily. Open a terminal, su to root, then issue (adapting with your own details, obviously!):
```console
pi64 ~ # useradd --create-home --groups "adm,disk,lp,wheel,audio,video,cdrom,usb,users,plugdev,portage,cron,gpio,i2c,spi" --shell /bin/bash --comment "Sakaki" sakaki
pi64 ~ # passwd sakaki
New password: <type your desired password for the new user, and press Enter>
Retype new password: <type the desired password again, and press Enter>
passwd: password updated successfully
```

You can then log out of `demouser`, and log into your new account, using the username and password just set up. When prompted with the `Welcome to the first start of the panel` dialog, select <kbd>Use default config</kbd>, as this provides a useful 'starter' layout.

> <a id="black_background_first_login"></a>Depending on how quickly you select <kbd>Use default config</kbd>, you *may* find you are left with a black desktop background. This issue should resolve itself after your next login, however.

Then, once logged in, you can delete the `demouser` account if you like. Open a terminal, `su` to root, then:
```console
pi64 ~ # userdel --remove demouser
userdel: demouser mail spool (/var/spool/mail/demouser) not found
```
> The mail spool warning may safely be ignored. Also, if you want your new user to be automatically logged in on boot (as `demouser` was), substitute your new username for `demouser` in the file `/etc/lightdm/lightdm.conf.d/50-autologin-demouser.conf` (and if you do _not_ want autologin, this file may safely be deleted).

One hint worth bearing in mind: desktop compositing is on for new users by default (unless you are using a Pi3 and have a very high-resolution display, when it will automatically be turned off for you). This gives nicely rendered window shadows etc. but if you find video playback framerates (from `VLC` etc.) are too slow, try turning it off (<kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>Window Manager Tweaks</kbd>, <kbd>Compositor</kbd> tab).

> Note that as of version 1.1.2 of the image, a number of Xfce 'fixups' will automatically be applied when a new user logs in for the first time; these include:<br>1) synchronizing compositing to the vertical blank (without which menus flash annoyingly under VC4);<br>2) setting window move and resize opacity levels; and<br>3) ensuring the desktop background displays on login (otherwise it often stays black; note that this fix sometimes does not work for the *first* login of a new user, as noted [above](black_background_first_login)).<br>They are managed by the `xfce-extra/xfce4-fixups-rpi3` package (which inserts an entry into `/etc/xdg/autostart/`).

## <a id="weekly_update"></a>Keeping Your System Up-To-Date

You can update your system at any time. As there are quite a few steps involved to do this correctly on Gentoo, I have provided a convenience script, `genup` ([source](https://github.com/sakaki-/genup), [manpage](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/genup.pdf)) to do this as part of the image.

Of course, you can always use `genup` directly (as root) to update your system at any time. See the [manpage](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/genup.pdf) for full details of the process followed, and the options available for the command.

> Bear in mind that a `genup` run will take around three to six hours to complete, even if all the necessary packages are available as binaries on the [binhost](#binhost). Why? Well, since Gentoo's [Portage](https://wiki.gentoo.org/wiki/Portage) - unlike most package managers - gives you the flexibility to specify which package versions and configuration (via USE flags) you want, it has a nasty graph-theoretic problem to solve each time you ask to upgrade (and it is mostly written in Python too, which doesn't help ^-^). Also, many of the binary packages are themselves quite large (for example, the current `libreoffice` binary package is >100MiB) and so time-consuming to download (unless you have a very fast Internet connection).

When an update run has completed, if prompted to do so by genup (directly, or at the tail of the `/var/log/latest-genup-run.log` logfile), then issue:
```console
pi64 ~ # dispatch-conf
```
to deal with any config file clashes that may have been introduced by the upgrade process.

For more information about Gentoo's package management, see [my notes here](https://wiki.gentoo.org/wiki/Sakaki's_EFI_Install_Guide/Installing_the_Gentoo_Stage_3_Files#Gentoo.2C_Portage.2C_Ebuilds_and_emerge_.28Background_Reading.29).

### Installing New Packages Under Gentoo

> The following is only a *very* brief intro to get you started. For more information, see the [Gentoo handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64/Working/Portage).

You can add any additional packages you like to your RPi. To search for available packages, you can use the (pre-installed) [`eix` tool](https://wiki.gentoo.org/Eix). For example, to search for all packages with 'hex editor' in their description:

```console
demouser@pi64 ~ $ eix --description --compact "hex.editor"
[N] app-editors/curses-hexedit (--): full screen curses hex editor (with insert/delete support)
[N] app-editors/dhex (--): ncurses-based hex-editor with diff mode
[N] app-editors/hexcurse (--): ncurses based hex editor
[N] app-editors/lfhex (--): A fast hex-editor with support for large files and comparing binary files
[N] app-editors/qhexedit2 (--): Hex editor library, Qt application written in C++ with Python bindings
[N] app-editors/shed (--): Simple Hex EDitor
[N] app-editors/wxhexeditor (--): A cross-platform hex editor designed specially for large files
[N] dev-util/bless (--): GTK# Hex Editor
Found 8 matches
```
(your output may vary).

Then, suppose you wanted to find out more about one of these, say `shed`:

```console
demouser@pi64 ~ $ eix --verbose app-editors/shed
* app-editors/shed
     Available versions:  *1.12 *1.13 ~*1.15
     Homepage:            http://shed.sourceforge.net/
     Find open bugs:      https://bugs.gentoo.org/buglist.cgi?quicksearch=app-editors%2Fshed
     Description:         Simple Hex EDitor
     License:             GPL-2

```
(again, your output may vary, depending upon the state of the Gentoo repo at the time you run the query, your version of `eix`, etc.)

That '\*' in front of versions 1.12 and 1.13 indicates that the package has *not* yet been reviewed by the Gentoo devs for the `arm64` architecture, but that its versions 1.12 and 1.13 *have* been reviewed as stable for some other architectures (in this case, `amd64`, `ppc` and `x86` - you can see this by reviewing the matching ebuilds, at `/usr/portage/app-editors/shed/shed-1.12.ebuild` and `/usr/portage/app-editors/shed/shed-1.13.ebuild`). Version 1.15 is also marked as being in testing (the '\~\*') for those (other) architectures.

However, you will find that most packages (which don't have e.g. processor-specific assembly or 32-bit-only assumptions) *will* build fine on `arm64` anyway, you just need to tell Gentoo that it's OK to try.

To do so for this package, become root, then issue:

```console
pi64 ~ # echo "app-editors/shed * ~*" >> /etc/portage/package.accept_keywords/shed
```

> The above means to treat as an acceptable build candidate any version marked as *stable* on any architecture (the '*\') or as *testing* on any architecture (the '\~\*'). The second does *not* imply the first - for example, `shed-1.12` is not marked as testing on any architecture at the time of writing, so just '~\*' wouldn't match it.

Then you can try installing it, using [`emerge`](https://wiki.gentoo.org/wiki/Portage):
```console
pi64 ~ # emerge --verbose app-editors/shed

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild  N    *] app-editors/shed-1.15::gentoo  86 KiB

Total: 1 package (1 new), Size of downloads: 86 KiB

>>> Verifying ebuild manifests
>>> Emerging (1 of 1) app-editors/shed-1.15::gentoo
>>> Installing (1 of 1) app-editors/shed-1.15::gentoo
>>> Recording app-editors/shed in "world" favorites file...
>>> Jobs: 1 of 1 complete                           Load avg: 1.25, 0.56, 0.21
>>> Auto-cleaning packages...

>>> No outdated packages were found on your system.

 * GNU info directory index is up-to-date.
```

Once this completes, you can use your new package!

> Note that if you intend to install complex packages, you may find it easier to set `ACCEPT_KEYWORDS="* ~*"` in `/etc/portage/make.conf`, since many packages are not yet keyworded for `arm64` yet on Gentoo. Obviously, this will throw up some false positives, but it will also mostly prevent Portage from suggesting you pull in 'live' (aka `-9999`, aka current-tip-of-source-control) ebuilds, which can easily create a nasty dependency tangle.

Once you get a package working successfully, you can then explicitly keyword its dependencies if you like (in `/etc/portage/package.accept_keywords/...`), rather than relying on `ACCEPT_KEYWORDS="* ~*"` in `/etc/portage/make.conf`. Should you do so, please feel free to file a PR against the [`genpi64:default/linux/arm64/17.0/desktop/genpi64` profile](https://github.com/sakaki-/genpi64-overlay/tree/master/profiles/targets/rpi3) with your changes, so that others can benefit (I intend to submit keywords from this profile to be considered for inclusion in the main Gentoo tree, in due course).

Have fun! ^-^

> If you need to install a particular package that isn't currently in the Gentoo tree, note that it _is_ possible to install binary packages from Raspbian (or other 32-bit repos) on your 64-bit system. For further details, please see [these notes](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Running-32-bit-Raspbian-Packages-on-your-64-bit-Gentoo-System).

> Alternatively, to install source packages with highly resource-intensive build profiles (such as `dev-lang/rust`), you can temporarily mount the image within a `binfmt_misc` QEMU user-mode `chroot` on your Linux PC, and perform the `emerge` there. This technique allows you to easily leverage the increased memory and CPU throughput of your regular desktop machine. For further details, please see my wiki tutorial [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Build-aarch64-Packages-on-your-PC%2C-with-User-Mode-QEMU-and-binfmt_misc).

## Miscellaneous Configuration Notes, Hints, and Tips (&darr;[skip](#maintnotes))

You don't need to read the following notes to use the image, but they may help you better understand what is going on!

* Because the Pi has no battery-backed real time clock (RTC), I have used the `swclock` (rather than the more usual `hwclock`) OpenRC boot service on the image - this simply sets the clock on boot to the recorded last shutdown time. An NTP client, `chronyd`, is also configured, and this will set the correct time as soon as a valid network connection is established.
   > Note that this may cause a large jump in the time when it syncs, which in turn will make the screensaver lock come on. Not a major problem, but something to be aware of, as it can be quite odd to boot up, log in, and have your screen almost immediately lock! For this reason, the `demouser` account on the image has the screensaver disabled by default.

* The image is configured with `NetworkManager`, so achieving network connectivity should be straightforward. The Ethernet interface is initially configured as a DHCP client, but obviously you can modify this as needed. Use the NetworkManager applet (top right of your screen) to do this; you can also use this tool to connect to a WiFi network.
   > Note that getting WiFi to work on the RPi3/4 requires appropriate firmware (installed in `/lib/firmware/brcm`): this is managed by the `sys-firmware/brcm43430-firmware` package (see [below](#wifi)). On the RPi3 B+ and RPi4 B, dual-band WiFi is supported.

* Bluetooth *is* operational on this image, but since, by default, the Raspberry Pi 3 [uses the hardware UART](http://www.briandorey.com/post/Raspberry-Pi-3-UART-Overlay-Workaround) / `ttyAMA0` to communicate with the Bluetooth adaptor, the standard serial console does not work and has been disabled (see `/etc/inittab` and `/boot/cmdline.txt`). You can of course [change this behaviour](https://www.raspberrypi.org/forums/viewtopic.php?t=138120) if access to the hardware serial port is important to you.
   > Bluetooth on the RPi3/4 also requires a custom firmware upload. This is carried out by a the `rpi3-bluetooth` OpenRC service, supplied by the `net-wireless/rpi3-bluetooth` package (see [below](#bluetooth)).
   
   * **Tip:** <a id="add_bt_device"></a>to connect a new Bluetooth device to your RPi3/4 (a wireless mouse or keyboard, for example), first click on the Bluetooth icon in the top toolbar. When the `Bluetooth Devices` panel opens, click on <kbd>Search</kbd>, and make your device discoverable (this will usually involve pressing or holding a button or switch on the device itself), shortly after which it should appear in the listing. Once it does, click on it, then press <kbd>Setup...</kbd>, then ensure the `Pair Device` radio button is selected, and click <kbd>Next</kbd>. Then, if you are attaching a more complex device such as a keyboard, you'll see a PIN code notification message displayed (simpler devices, such as mice, often don't require a PIN for pairing, in which case no message will show). If you *are* prompted with a PIN, enter the code given into your device (for a keyboard, this normally means typing it in and pressing <kbd>Enter</kbd>). Once the devices are paired, you'll be prompted where to connect the device to: ensure the `Human Interface Device Service (HID)` radio button is selected, and click <kbd>Next</kbd>. Hopefully you will then see a message telling you `Device added and connected successfully`; click <kbd>Close</kbd> to dismiss it. With this done, your new device should start working. However, if you want to have it connect again automatically each boot, there's one more step you need to do. Click on your new device in the list (its icon should now be showing a key symbol, indicating that it is paired), and then click on the <kbd>Trust</kbd> shortcut button (the one that looks like a four-pointed gold star). With this done, your device setup is complete! You can add multiple interface devices to your RPi3/4 in this manner.

* The Pi uses the first (`vfat`) partition of the microSD card (`/dev/mmcblk0p1`) for booting. The various (closed-source) firmware files pre-installed there may are managed by the `sys-boot/rpi3-64bit-firmware` package (see [below](#boot_fw)). Once booted, this first partition will be mounted at `/boot`, and you may wish to check or modify the contents of the files `/boot/cmdline.txt` and `/boot/config.txt`.

* Firefox is not currently provided, but *may* be in a future release.  

* As of Alpha1 of the image, Chromium is not provided.  Chromium dropped jumbo-build support and takes days to compile.
* The currently recommended browser is Falkon, a QTWebEngine based lightweight browser.

* No mail client is pre-installed, but binpkgs for thunderbird and evolution should soon be available.

* By default, the image uses the Pi's [VC4 GPU](https://wiki.gentoo.org/wiki/Raspberry_Pi_VC4) acceleration (VC6 in the Pi4, although it still uses the `vc4...` kernel modules) in X, which has reasonable support in the 4.19.y kernel and the supplied userspace (via `media-libs/mesa` ultimately - this has been [tweaked](https://github.com/sakaki-/genpi64-overlay/blob/a8e84d5295bd49811456a953514521ba5efa01ba/media-libs/mesa/mesa-19.1.4-r1.ebuild#L413) 
on the image to include v3d support for the new Pi4). Note however that this is still work in progress, so you may experience issues with certain applications. The image (as shipped) uses the mixed-mode [`vc4-fkms-v3d`](https://www.raspberrypi.org/forums/viewtopic.php?f=29&t=159853) overlay / driver (see `/boot/config.txt`, or <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>RPi Config Tool</kbd>; you can change the driver if you wish to, or are experiencing serious glitches). On my RPi4 B at least, a `glxgears` score significantly in excess 1000fps can be obtained on an unloaded system  (~400fps on a loaded desktop). Incidentally, in normal use the main load is the Xfce window manager's compositor - try turning it off to see (via <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>Window Manager Tweaks</kbd>, <kbd>Compositor</kbd> tab) . YMMV - if you experience problems with this setup (or find that e.g. your system is stuck on the 'rainbow square' at boot time after a kernel update), you can fall-back to the standard framebuffer mode by commenting out the `dtoverlay=vc4-fkms-v3d` line in `/boot/config.txt` (i.e., the file `config.txt` located in the microSD card's first partition).
   > Kodi, VLC and SMPlayer have been pre-installed on the image, and they will all make use of accelerated (Mesa/gl) playback. Because of incompatibilities and periodic crashes when using it, XVideo (xv) mode has been disabled in the X server (`/etc/X11/xorg.conf.d/50-disable-Xv.conf`); this doesn't really impinge on usability. NB: **if you find video playback framerates too slow**, it is always worth **disabling the window manager's compositor** (see point immediately above) as a first step. Hint: for some video types, SMPlayer and Kodi perform significantly better than VLC, so if you do experience 'glitchy' playback with one media player, try using one of the others.

* The frequency governor is switched to `ondemand` (from the default, `powersave`), for better performance, as of version 1.0.1. This is managed by the `sys-apps/rpi3-ondemand-cpufreq` package (see [below](#ondemand)).
  * If you are using an RPi3 Model B board, the frequency will switch dynamically between 600MHz and 1.2GHz, depending on load. On a Model B+, it will switch between 600MHz and 1.4GHz. On an RPi4, it will switch between 600MHz and 1.5GHz.

* On the RPi4, you can easily try *overclocking* your system, without voiding your warranty. To do so, open <kbd>Applications</kbd>&rarr;<kbd>Settings</kbd>&rarr;<kbd>RPi Config Tool</kbd> app, select the <kbd>Pi4 Tuning</kbd> tab, select an option from `Mid` to `Extreme` in the supplied 'manettino' ^-^, click <kbd>Save and Exit</kbd>, then elect to reboot when prompted. Note that you are likely to require active cooling for your RPi4 when overclocked, and it is always possible (although unlikely) that your device may fail to boot at the faster settings (if it does, simply edit the file `config.txt` on the first partition of the microSD card, comment out the `arm_freq` and `gpu_freq` settings, and try again).

* On the RPi4, please make sure your EEPROM firmware is up-to-date; there is (at the time of writing) an official update available (`0137a8.bin`) that can lower power consumption by around 300mW, which makes a significant difference to this somewhat [thermally challenged](https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=245703) ^-^ device. For more details please see [this post](https://www.raspberrypi.org/forums/viewtopic.php?p=1490467#p1490467).

* `PermitRootLogin yes` has explicitly been set in `/etc/ssh/sshd_config`, and `sshd` is present in the `default` runlevel. This is for initial convenience only - feel free to adopt a more restrictive configuration.

* I haven't properly tested suspend to RAM or suspend to swap functionality yet.

* As of version 1.0.1, all users in the `wheel` group (which includes `demouser`) have a passwordless sudo ability for all commands. Modify `/etc/sudoers` via `visudo` to change this, if desired (the relevant line is `%wheel ALL=(ALL) NOPASSWD: ALL`).

* The image uses the RPi3/4's hardware random number generator to feed `/dev/random` (via `sys-apps/rng-tools`); see [these notes](https://github.com/sakaki-/genpi64-overlay/tree/master/sys-boot/rpi3-64bit-firmware) for further details.

* As a (limited, 64-bit) build of `media-libs/raspberrypi-userland` is included, you can use `vcgencmd` etc. As of version 1.5.2 of the image, this includes support for MMAL (including bundled tools such as `raspivid` and `raspicam`), although OpenMAX-IL is not yet supported from a 64-bit userland.

* If you wish to share files on a network with Windows boxes, please note that `net-fs/samba` is bundled as of version 1.2.2 of the image. To configure and activate it, please see e.g. [these notes](https://forums.gentoo.org/viewtopic-p-8215174.html#8215174).

* An `sysctl.d` rule (`35-low-memory-cache-settings.conf`) is used to modify the kernel cache settings slightly, for better performance in a memory constrained environment, per [these notes](https://www.codero.com/knowledge-base/pdf.php?cat=3&id=388&artlang=en).

* You can easily install (and run) 32-bit Raspbian apps on your 64-bit Gentoo system if you like (using `apt-get` from a `chroot`), since [ARMv8 supports mixed-mode userland](https://community.arm.com/processors/f/discussions/3330/how-aarch32-bit-applications-will-be-supported-on-aarch64?ReplyFilter=Answers&ReplySortBy=Answers&ReplySortOrder=Descending). If this interests you, please see my wiki entry on this subject, [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Running-32-bit-Raspbian-Packages-on-your-64-bit-Gentoo-System).

* To install source packages with resource-intensive build profiles, you can temporarily mount the image within a `binfmt_misc` QEMU user-mode `chroot` on your PC, and perform the `emerge` there. For further details, please see my wiki tutorial [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Build-aarch64-Packages-on-your-PC%2C-with-User-Mode-QEMU-and-binfmt_misc).

* If you would like to run **benchmarks** on your RPi3/4 system, please see [these notes](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Effective-Benchmarking-on-your-64-Bit-RPi-System).

* You can easily remove unwanted applications, and even the Xfce4 graphical desktop itself, from your 64-bit Gentoo system if desired. To do so, please refer to my notes [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Create-a-Slimmed-Down-Version-of-the-Image).

* If you'd like to set up a persistent dual-monitor setup on an RPi4, please see my short tutorial on this project's open wiki [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Dual-Displays-on-your-RPi4) (also contains some notes useful for single-display systems too).

## <a id="maintnotes"></a>Maintenance Notes (Advanced Users Only) (&darr;[skip](#miscpoints))

The following are some notes regarding optional maintenance tasks. The topics here may be of interest to advanced users, but it is not necessary to read these to use the image day-to-day.

### <a id="heavylifting"></a>Have your Gentoo PC Do the Heavy Lifting!

The RPi3 (and even the RPi4, although significantly better) does not have a particularly fast processor when compared to a modern PC. While this is fine when running the device in day-to-day mode (as a IME-free lightweight desktop replacement, for example), it does pose a bit of an issue when building large packages from source.
> Of course, as the (>= 1.1.0) image now has a weekly-autobuild [binhost](#binhost) provided, this will only really happen if you start setting custom USE flags on packages like `app-office/libreoffice`, or emerging lots of packages not on the [original pre-installed set](https://github.com/sakaki-/gentoo-on-rpi-64bit/blob/master/reference/world-all-packages).

However, there is a solution to this, and it is not as scary as it sounds - leverage the power of your PC (assuming it too is running Gentoo Linux) as a cross-compilation host!

For example, you can cross-compile kernels for your RPi3/4 on your PC very quickly (around 5-15 minutes from scratch), by using Gentoo's `crossdev` tool. See my full instructions [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Your-Gentoo-PC-for-Cross-Compilation-with-crossdev), [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Build-an-RPi4-64bit-Kernel-on-your-crossdev-PC) and [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Build-an-RPi3-64bit-Kernel-on-your-crossdev-PC) on this project's open [wiki](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki).

Should you setup `crossdev` on your PC in this manner, you can then take things a step further, by leveraging your PC as a `distcc` server (instructions [here](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Your-crossdev-PC-for-Distributed-Compilation-with-distcc) on the wiki). Then, with just some simple configuration changes on your RPi3 (see [these notes](https://github.com/sakaki-/gentoo-on-rpi-64bit/wiki/Set-Up-Your-RPi3-as-a-distcc-Client)), you can distribute C/C++ compilation (and header preprocessing) to your remote machine, which makes system updates a lot quicker (and the provided tool `genup` will automatically take advantage of this distributed compilation ability, if available).

> Since they share the same ARMv8a architecture (`march`), binaries built in this manner can be used on either a RPi3 Model B, B+ *or* an RPi4 Model B, without modification.

### <a id="rpi3_headless"></a>Using your RPi3/4 as a Headless Server

If you want to run a dedicated server program on your RPi3 or RPi4 (for example, a cryptocurrency miner), you won't generally want (or need) the graphical desktop user interface. To disable it, issue (as root):
```console
pi64 ~ # rc-update del xdm default
```

<a id="reclaim_mem"></a>You can also reclaim the memory reserved for the `vc4` graphics driver (this is optional). To do so, issue:
```console
pi64 ~ # nano -w /boot/config.txt
```

and comment out the following line, so it reads:
```ini
#dtoverlay=vc4-fkms-v3d
```

(Your setup may have a `cma` specification following the `vc4-fkms-v3d`; it's still the same line, and should be commented.)

Also ensure that the camera module support software is not loaded, and that only a minimal amount of GPU memory is allocated - to do so, ensure the below lines in the file read as follows:
```ini
#start_x=1
gpu_mem=16
```

Leave the rest of the file as-is. Save, and exit `nano`.

Reboot your system. You will now have a standard terminal login available only, which greatly saves on system resources.

It is generally more reliable to use the Ethernet rather than the WiFi network interface when running a headless server in this manner. Also, make sure your system has an adequate heatsink fitted (or even active CPU cooling, for particularly demanding applications), since the RPi3 (particuarly when running in 64-bit mode) can [get quite hot](https://www.theregister.co.uk/2017/10/18/active_cooling_a_raspberry_pi_3/), leading to thermal throttling or even protective system shutdown if your cooling is insufficient. The RPi3 Model B+ has a [integral heat-spreader](https://core-electronics.com.au/tutorials/raspberry-pi-3-model-b-plus-performance-vs-3-model-b.html), so (in addition to having a higher clock speed) it may be a somewhat better choice than the RPi3 Model B for more demanding applications. The RPi4 has [even more significant thermal challenges](https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=245703) than the RPi3, so active cooling is recommended for demanding applications.

> Of course, take the normal precautions when running an internet-exposed server in this manner: for example, set up an appropriate firewall, consider running the server process under `firejail`, modify the shipped-default passwords, and keep your system up-to-date by (manually) running `genup`, from time to time.

Should you wish to revert back to using the graphical desktop at some point in the future, simply issue (as root):
```console
pi64 ~ # rc-update add xdm default
```

Then, if you edited `/boot/config.txt` [above](#reclaim_mem), undo that change now. Issue:
```console
pi64 ~ # nano -w /boot/config.txt
```

and uncomment the following line, so it reads:
```ini
dtoverlay=vc4-fkms-v3d
```

> With modern boot firmware, the recommendation is *not* to set an explicit `cma` parameter on `vc4-fkms-v3d`.

If you wish to use the camera, and hardware video codecs, also ensure that the below lines read as follows (if not, this step may be omitted):
```ini
start_x=1
gpu_mem=128
```

Leave the rest of the file as-is. Save, and exit `nano`. Reboot your system, and you should have your full graphical desktop back again!


## <a id="miscpoints"></a>Miscellaneous Points (Advanced Users Only) (&darr;[skip](#helpwanted))

The following are some notes about the detailed structure of the image. It is not necessary to read these to use the image day-to-day. <!--  -->

### Subscribed Ebuild Repositories (_aka_ Overlays)

The image is subscribed to the following ebuild repositories:
* **gentoo**: this is the main Gentoo tree of course, but is (by default) supplied via `rsync://isshoni.org/gentoo-portage-pi64-gem`, a weekly-gated, signature-authenticated portage rsync mirror locked to the binhost's available files, as described [above](#rsync).
* **[genpi-tools](https://github.com/GenPi64/genpi-tools)**: this provides a number of small utilities for Gentoo. The image currently uses the following ebuilds from the `genpi-tools` overlay:
  * **app-portage/showem** [source](https://github.com/GenPi64/showem), [manpage](https://github.com/GenPi64/gentoo-on-rpi-64bit/blob/master/reference/showem.pdf)

    A tool to view the progress of parallel `emerge` operations.
  * **app-portage/genup** [source](https://github.com/GenPi64/genup), [manpage](https://github.com/GenPi64/gentoo-on-rpi-64bit/blob/master/reference/genup.pdf)

* **[genpi64](https://github.com/GenPi64/genpi64-overlay)**: this overlay (which used to be named `rpi3` and was subsequently [migrated](https://forums.gentoo.org/viewtopic-p-8362116.html#8362116) at v1.5.0 with the advent of RPi4 support) provides ebuilds specific to the Raspberry Pi 3 & Pi 4, or builds that have fallen off the main Gentoo tree but which are the last known reliable variants for `arm64`. It also provides the [custom profile](#profile) [`genpi64:default/linux/arm64/17.0/desktop/genpi64`](hhttps://github.com/GenPi64/genpi64-overlay/tree/master/profiles/targets/genpi64). A list of provided ebuilds may be found on the [overlay's project page](https://github.com/GenPi64/genpi64-overlay#list_of_ebuilds).

## <a id="projectwiki"></a>Project Wiki

In addition to the notes in this README, this project also has an associated open **wiki**, containing a number of short tutorial articles you may find helpful when using 64-bit Gentoo on your RPi3 or RPi4.

You can view the wiki homepage [here](https://github.com/GenPi64/gentoo-on-rpi-64bit/wiki). Feel free to add your own material!
  
## <a id="helpwanted"></a>Help Wanted!

You've got this far through the README - I'm impressed ^-^. In fact, you may be just the sort of person to help get `arm64` into a more stable state in the main Gentoo tree...

## Acknowledgement

I'd like to acknowledge NeddySeagoon's work getting Gentoo to run in 64-bit mode on the RPi3 (see particularly [this thread](https://forums.gentoo.org/viewtopic-t-1041352.html), which was a really useful reference when putting this project together originally).

## Feedback Welcome!

If you have any problems, questions or comments regarding this project, please either use our Discord, or try to get in touch with us on IRC (#gentoo-arm, Freenode).


