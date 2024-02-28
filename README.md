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

-- removing adb? and other kernel staff related with luckfox original mini-a software

