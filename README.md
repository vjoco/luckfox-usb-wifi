# luckfox-usb-wifi
wifi usb support for luckfox-mini-a, on an alpine linux port

## REQUIRED: 
* know how to port alpine, https://wiki.luckfox.com/Luckfox-Pico/Luckfox-Pico-Alpine-Linux-2/
* know how to activate usb host in the dts , https://wiki.luckfox.com/Luckfox-Pico/Luckfox-Pico-USB
* know to use the sdk https://wiki.luckfox.com/Luckfox-Pico/Luckfox-Pico-SDK
* know to modify the kernel menuconfig https://wiki.luckfox.com/Luckfox-Pico/Luckfox-Pico-Menuconfig

# describing steps:

## clone repository (if needed), build, enable usb host 
git clone https://github.com/LuckfoxTECH/luckfox-pico

building for mini_a board
-- change dts to enable usb host
Enter the device tree file path and open the device tree file corresponding to LuckFox Pico .... The file path is:

Luckfox Pico Mini A: <SDK directory>/sysdrv/source/kernel/arch/arm/boot/dts/rv1103g-luckfox-pico-mini-a.dts

Modify the relevant device tree to set the USB mode to host mode.

&usbdrd_dwc3 {
    status = "okay";
    dr_mode = "host";
};

##
enable USB USB_EHCI_HCD, and SCSI as described in https://wiki.luckfox.com/Luckfox-Pico/Luckfox-Pico-USB

## Enable kernel support for your wifi usb

I've done it for a Ralink RT5370 and an RTL8192CU

Location:                                                                                                        x
  x     -> Device Drivers                                                                                              x
  x       -> Network device support (NETDEVICES [=y])                                                                  x
  x         -> Wireless LAN (WLAN [=y])                                                                                x
  x           -> Realtek devices (WLAN_VENDOR_REALTEK [=y])                                                            x
  x (1)         -> Realtek rtlwifi family of devices (RTL_CARDS [=m]) 

search Ralink for the rt5370.... For ralink I could insert the module in the kernel
 for rtl8192cu i loaded at the boot time

## for alpine
modify /etc/fstab to include the oem mount

it should have an extra line like:

/dev/mmcblk1p5          /oem            ext4    defaults        0       0


in the docker, enable linux-firmware-xxx suport before packing:

apk add linux-firmware-rtlwifi ( for rt9192cu)

apk del linux-firmware-other (for ralink rt5370) 

!!! remove unnecessary bin files from /lib/firmware before packing... i kept only the rt5370.bin

apk add wpa_supplicant

start wpa_supplicant service

rc-update add wpa_supplicant

create a /var/run/wpa_supplicant/ folder

create a /etc/wpa_supplicant/wpa_supplicant.conf file for your connection

edit the /etc/network/interfaces accordingly ... the name of the usb connection was always wlan0

you should get something like (dmesg):

` * Setting hostname ... [ ok ]
 * Starting WPA Supplicant ... [ ok ]
 * Starting networking ...[    2.550312] ieee80211 phy0: rt2x00lib_request_firmware: Info - Loading firmware file 'rt2870.bin'
[    2.553051] ieee80211 phy0: rt2x00lib_request_firmware: Info - Firmware detected - version: 0.36
 *   lo ... [ ok ]
 *   wlan0 ...udhcpc: started, v1.36.1
udhcpc: broadcasting discover
[    4.021231] wlan0: authenticate with 3c:7c:3f:c4:ef:28
[    4.040420] wlan0: send auth to 3c:7c:3f:c4:ef:28 (try 1/3)
[    4.042239] wlan0: authenticated
[    4.043311] wlan0: associating with AP with corrupt probe response
[    4.046348] wlan0: associate with 3c:7c:3f:c4:ef:28 (try 1/3)
[    4.066365] wlan0: RX AssocResp from 3c:7c:3f:c4:ef:28 (capab=0xc11 status=0 aid=2)
[    4.071645] wlan0: associated
udhcpc: broadcasting discover
udhcpc: broadcasting select for 192.168.50.212, server 192.168.50.1
udhcpc: lease of 192.168.50.212 obtained from 192.168.50.1, lease time 86400
 [ ok ]
 * Starting sshd ... [ ok ]

Welcome to Alpine Linux 3.19
Kernel 5.10.110 on an armv7l (ttyFIQ0)

tools:~# ping google.com
PING google.com (216.58.215.142): 56 data bytes
64 bytes from 216.58.215.142: seq=0 ttl=115 time=25.335 ms
64 bytes from 216.58.215.142: seq=1 ttl=115 time=32.477 ms
64 bytes from 216.58.215.142: seq=2 ttl=115 time=21.694 ms

`




























