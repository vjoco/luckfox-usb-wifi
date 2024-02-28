# luckfox-usb-wifi
wifi usb support for luckfox-mini-a, on an alpine linux port

## describing steps:

git clone https://gitee.com/LuckfoxTECH/luckfox-pico.git
or 
https://github.com/LuckfoxTECH/luckfox-pico

building for mini_a board

-- change dts to enable usb host
Enter the device tree file path and open the device tree file corresponding to LuckFox Pico .... The file path is:

Luckfox Pico Mini A: <SDK directory>/sysdrv/source/kernel/arch/arm/boot/dts/rv1103g-luckfox-pico-mini-a.dts
 ![image](https://github.com/vjoco/luckfox-usb-wifi/assets/16479426/f2438470-73df-4eba-9802-fd0303fa9090)

Modify the relevant device tree to set the USB mode to host mode.

&usbdrd_dwc3 {
    status = "okay";
    dr_mode = "host";
};

scsi support

![image](https://github.com/vjoco/luckfox-usb-wifi/assets/16479426/19013121-4b07-4026-83d1-3b0fbde8fd35)

![image](https://github.com/vjoco/luckfox-usb-wifi/assets/16479426/fd7dda83-bdae-433f-a9fa-6be18b901895)

and USB_EHCI_HCD

Location:                                                                                                        x
  x     -> Device Drivers                                                                                              x
  x       -> Network device support (NETDEVICES [=y])                                                                  x
  x         -> Wireless LAN (WLAN [=y])                                                                                x
  x           -> Realtek devices (WLAN_VENDOR_REALTEK [=y])                                                            x
  x (1)         -> Realtek rtlwifi family of devices (RTL_CARDS [=m]) 

------ kernel options steps

cd sysdrv/source/kernel/

cp ./arch/arm/configs/luckfox_rv1106_linux_defconfig .config

make ARCH=arm menuconfig

-after the changes

make ARCH=arm savedefconfig

cp defconfig ./arch/arm/configs/luckfox_rv1106_linux_defconfig

cd ../../../

./build.sh kernel

 boot.img was created


-- removing adb? and other kernel staff related with luckfox original mini-a software

