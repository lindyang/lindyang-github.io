---
title: "Kernel"
date: 2024-01-12T11:03:26+08:00
tags: [misc]
---

```

drivers/net/ethernet/stmicro/stmmac/
    dwmac-generic.c
    stmmac_main.c
    stmmac_platform.c
    stmmac_platform.h
    Kconfig
    Makefile
    dwmac-phytium.c     (new file)

drivers/gpio/
    Kconfig
    Makefile

drivers/net/phy/
    at803x.c

sound/pci/hda/
    hda_controller.c
    hda_phytium.h       (new file)
    hda_phytium.c       (new file)
    Kconfig
    Makefile

sound/hda/
    hdac_controller.c
    hdac_stream.c

drivers/usb/host/
    xhci-mem.c
    xhci-pci.c
    xhci.h
        +#define XHCI_SLOWDOWN_QUIRK     BIT_ULL(33)   有问题


##############
5.10.103-sound-pci-hda.patch
sound/pci/hda/
    Kconfig
    Makefile
    hda_controller.c
        @@ -157,6 +159,10
        ->
        @@ -156,6 +159,10
    hda_phytium.h   ++++
    hda_phytium.c   ++++


5.10.106-include-sound-hdaudio.h.patch
include/sound/
    hdaudio.h
        @@ -340,6 +340,7 @@
        ->
        @@ -342,6 +340,7 @@

5.10.106-sound-hda.patch
sound/hda/
    hdac_controller.c
    hdac_stream.c
        @@ -521,7 +525,11
        ->
        @@ -520,7 +525,11


5.10.92-drivers-net-ethernet-stmicro-stmmac.patch
drivers/net/ethernet/stmicro/stmmac/
    dwmac-generic.c
        @@ -85,6 +92,17
        ->
        @@ -84,6 +92,17

        @@ -92,6 +110,7
        ->
        @@ -91,6 +110,7
    stmmac_main.c
        26a27       ????
    stmmac_platform.c
        #include <linux/pm_runtime.h>
        #include <linux/module.h>
        ->
        #include <linux/module.h>
        #include <linux/io.h>

        @@ -657,6 +660,248
        ->
        @@ -640,6 +660,248

        @@ -664,8 +909,14
        ->
        @@ -647,8 +909,14

        @@ -683,6 +934,7
        ->
        @@ -666,6 +934,7
    stmmac_platform.h


5.10.92-drivers-net-phy-at803x.c.patch
drivers/net/phy/
    at803x.c
        @@ -196,6 +200,20 @@
        ->
        @@ -204,6 +200,20 @@

        @@ -575,6 +593,10 @@
        @@ -627,6 +593,10 @@




gpiochip_set_chained_irqchip


Device Drivers -> N(e)twork device support -> (E)thernet driver support
    I210 Gigabit Network Connection/igb_uio/pci@0000:05:00.0
        /E1000E
        M或* Intel(R) PRO/1000 PCI-Exrpess Gigabit Ethernet support
    enaphyt4i0/st_gmac
        /stmicro
        M或* Phytium dwmac support (NEW)

Device Drivers -> (S)ound card support -> Advanced Linux Sound Architechture ---> H(D)-Audio ---> <M> (P)HYTIUM HD Audio

Device Drivers -> <disable> MMC/SD/SDIO card support -->

(G)PIO Support -> Memory mapped GPIO drivers ---> 最后两项

Networking support ---> Wireless ->
Wi-Fi 6 AX210/AX211/AX411 160MHz
https://community.frame.work/t/solved-using-the-ax210-with-linux-on-the-framework-laptop/1844
intel AX210 driver download
https://www.intel.com/content/www/us/en/support/articles/000005511/wireless.html


sudo apt-get install openssl \
    libssl-dev build-essential \
    openssl pkg-config libc6-dev bison \
    flex libelf-dev zlibc minizip;

sudo apt-get install libidn11-dev libidn11 zstd;

sudo apt install libncurses5-dev;



make menuconfig;
# CONFIG_SYSTEM_TRUSTED_KEYS="";
make -j8;
# 编译安装组件
make INSTALL_MOD_STRIP=1 modules_install -j8;
# 安装内核
make  install -j8;
# 安装头文件
make headers_install INSTALL_HDR_PATH=/usr;

# build & source
apt install flex bison -y;
# .config Module.symvers;
make oldconfig && make prepare && make scripts;
unlink /usr/lib/modules/4.19.272/build;
unlink /usr/lib/modules/4.19.272/source;
ln -s /usr/src/linux-phytium-main/ /usr/lib/modules/4.19.272/build;
ln -s /usr/src/linux-phytium-main/ /usr/lib/modules/4.19.272/source;


# 内核升级:
# cd ./core
# 解压编译后的内核到tmp下
# tar -xf linux-phytium-4.19.272-wifi-make.tgz -C /tmp
# cd /tmp/linux-phytium-main
# make INSTALL_MOD_STRIP=1  modules_install -j8
# make  install -j8
# 生成头文件
# make headers_install INSTALL_HDR_PATH=/usr
# 将内核的源代码放到/usr/src（否则编译会报错）
# apt install flex bison -y
# cd /usr/src/linux-phytium-main
# cp /tmp/linux-phytium-main/.config /usr/src/linux-phytium-main/
# cp /tmp/linux-phytium-main/Module.symvers /usr/src/linux-phytium-main/
# make oldconfig && make prepare && make scripts;
# unlink /usr/lib/modules/4.19.272/build
# unlink /usr/lib/modules/4.19.272/source
# ln -s /usr/src/linux-phytium-main/ /usr/lib/modules/4.19.272/build
# ln -s /usr/src/linux-phytium-main/ /usr/lib/modules/4.19.272/source
# reboot
```
