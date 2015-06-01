---
layout: post
title: Cuda and Acer Chromebook 13
---

Latest (20 May 2015) update of chromeos for Acer Chromebook 13 changed the nvidia built in driver and cuda is no longer supported.
This post will show how to compile a working kernel from chrubuntu 
-----

###Steps
0. Download couple of packages: 

	sudo apt-get install u-boot-tools device-tree-compiler vboot-kernel-utils

1. From chrubuntu clone git repo for kernel.  This version looks like it's working ok: release-R41-6680.B-chromeos-3.10 [3]

	git clone https://chromium.googlesource.com/chromiumos/third_party/kernel -b release-R41-6680.B-chromeos-3.10

2. Get firmware package for xhci

	wget http://commondatastorage.googleapis.com/chromeos-localmirror/distfiles/xhci-firmware-2014.11.06.00.00.tbz2
	tar xf xhci-firmware-2014.11.06.00.00.tbz2
	cd kernel
	cp -r ../lib/firmware .

3. Set build environment.  We are using chrubuntu directly, hence there is no need for cross compilation, but good instructions are in [1] and [2]

	export WIFIVERSION="-3.8"
	export ARCH=arm

4. Set up initial config

	./chromeos/scripts/prepareconfig chromeos-tegra

5. Run menuconfig to set firmware location Device Driver / General Options / External firmware location: nvidia/tegra124/xusb.bin

	make menuconfig

6. Finaly compile the kernel and modules.  Get a cup of coffee or a tea

	make -j 4 zImage modules
	make tegra124-nyan-big.dtb

7. Build kernel image

	wget https://raw.githubusercontent.com/lgeek/gnu-linux-on-acer-chromebook-13/master/nyan-big-fit.cfg
	mkimage -f nyan-big-fit.cfg nyan-big-kernel
	echo "noinitrd console=tty1 debug verbose root=/dev/mmcblk1p7 rootwait rw lsm.module_locking=0 net.ifnames=0 rootfstype=ext4" > ./cmdline
	vbutil_kernel --arch arm --pack kernel.bin --keyblock /usr/share/vboot/devkeys/kernel.keyblock --signprivate /usr/share/vboot/devkeys/kernel_data_key.vbprivk --version 1 --config cmdline --vmlinuz nyan-big-kernel

8. Install modules and kernel.  In my case boot partition is mmcblk1p6:

	sudo WIFIVERSION=-3.8 ARCH=arm make modules_install
	dd if=./kernel.bin of=/dev/mmcblk1p6
	sudo dd if=./kernel.bin of=/dev/mmcblk1p6
	sudo reboot

9. Enjoy your cup of coffe (or tea) while running cuda computations.

[1] https://github.com/lgeek/gnu-linux-on-acer-chromebook-13  
[2] http://www.vctlabs.com/posts/2015/Jan/22/tegra_chromebook_hack/  
[3] https://groups.google.com/a/chromium.org/forum/#!topic/chromium-os-dev/u9DmbSYWduo  
