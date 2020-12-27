---
title: "DFU"
date: 2020-07-04T09:00:20+08:00
weight: 20
---

The STM32L432 bootloader supports [USB DFU protocol](https://www.st.com/resource/en/application_note/cd00264379-usb-dfu-protocol-used-in-the-stm32-bootloader-stmicroelectronics.pdf).

You can use [dfu-utils](https://github.com/z4yx/dfu-util) or [Web DFU](https://dfu.canokeys.org/) to flash firmware onto your self-made CanoKey or Nucleo-L432KC board.

## Entering DFU mode

### Using Web Console

Currently, only Chromium / Google Chrome supports WebUSB, so you should use Chromium or Google Chrome to access [Web Console](https://console.canokeys.org/) and [Web DFU](https://dfu.canokeys.org/).

* Visit Canokey Web Console https://console.canokeys.org/ using Chromium or Google Chrome.
* Click Connect on the top right corner of the page, then select "Canokey".
* After the basic information shows up, click "GO" in the Admin area.
* Click "AUTHENTICATE" to authenticate your key in admin privileges. The default admin password is `123456` if you haven't changed it before. If you've typed the right key, a green promot "PIN verification success" will show up in the bottom left and more configurable areas shows up in the page.
* Click "ENTER DFU (DEVELOPMENT ONLY)" to make CanoKey reboot to DFU mode

**Do not unplug your CanoKey after entering DFU, otherwise you will need to repeat the steps again!**

### Using script for Linux

This is not suggested as you need to know how to find your reader name of the CanoKey.

* Make sure the command `scriptor` and `pcsc_scan` is available in your system. If not, install them from your package manager.
* Download the [enter-dfu.sh](https://github.com/canokeys/canokey-stm32/blob/master/enter-dfu.sh) script from canokey-stm32.
* Use `pcsc_scan` command to identify the reader name. In my case it is `Kingtrust Multi-Reader [OpenPGP PIV OATH] (00000000) 00 00`
* Run `enter-dfu.sh "Kingtrust Multi-Reader [OpenPGP PIV OATH] (00000000) 00 00"`. If you see output like the following, then the CanoKey went into DFU successfully. The `Can't get info: Transaction failed` can be safely ignored in this scenario.

```
$ ./enter-dfu.sh "Kingtrust Multi-Reader [OpenPGP PIV OATH] (00000000) 00 00"
Reader name: Kingtrust Multi-Reader [OpenPGP PIV OATH] (00000000) 00 00
Using given card reader: Kingtrust Multi-Reader [OpenPGP PIV OATH] (00000000) 00 00
Using T=1 protocol
Reading commands from STDIN                                                                                                                                                                   
> 00 A4 04 00 05 F0 00 00 00 00                                                                                                                                                               
< 90 00 : Normal processing.                                                                                                                                                                  
> 00 20 00 00 06 31 32 33 34 35 36                                                                                                                                                            
< 90 00 : Normal processing.                                                                                                                                                                  
> 00 FF 22 22                                                                                                                                                                                 
Can't get info: Transaction failed. 
```

**Do not unplug your CanoKey after entering DFU, otherwise you will need to repeat the steps again!**

### Additional steps for Windows

You need to manually change the driver to make DFU work under Windows. Follow the following steps.

* Download [Zadig](https://zadig.akeo.ie/). Note, Zadig is provided by a third party, which CanoKeys team have no direct affiliation with.
* Close your Chromium / Google Chrome
* Run Zadig, click the `Options` menu, then click `List All Devices`.
* Choose `STM32 BOOTLOADER` from the main window
* In the `Driver` line, change the right side to WinUSB.
* Click `Reinstall WICD Driver`

After that, you should be able to use DFU to flash your firmware.


## Update firmware with DFU

Make sure the DFU is recognised by your system. You should see a USB device with VID:PID `0483:df11` in your system. In my scenario, it is

```
$ lsusb | grep 0483:df11
Bus 003 Device 013: ID 0483:df11 STMicroelectronics STM Device in DFU Mode
```
### Use Web DFU to flash the firmware

* Go to web DFU site https://dfu.canokeys.org/
* Make sure the 'Vendor ID (hex)' is 0x0483. Click Connect
* Choose `STM32 BOOTLOADER` from the prompt
* Then you should see the web page asks you to select interface. Choose Internal Flash (Such as `DFU: cfg=1, intf=0, alt=0, name="@Internal Flash /0x08000000/0128*0002Kg"`) and click "Select interface"
* The basic DFU information of the device will show up. Then you can use 'Firmware Download' to write new firmware to your CanoKey. Click "Choose file" to select your firmware, then click 'Download'
* After the firmware downloaded, you are ready to use your CanoKey.

### Use dfu-util

The official dfu-util have some issue downloading firmware to STM32L432 sometimes, so we suggest you to use [this patched version](https://github.com/z4yx/dfu-util).

```
/path/to/your/dfu-util -i 0 -a 0 -D /path/to/your/canokey.bin -s 0x08000000
```
Replace /path/to/your/dfu-util with the path to your dfu-util, and /path/to/your/canokey.bin with the path to the firmware you want to flash.

## FAQ

* Question: Chromium / Google Chrome did not recognise my CanoKey when I am trying in the Web Console
  * Answer: Make sure you have proper permission to access CanoKey, and also check if Chromium / Google Chrome have proper permission. Or you may want to use root to run your Chromium browser, like `sudo chromium-browser --no-sandbox`.
* Question: I get `Failed to get Canokey version: Device busy` error when connecting to the CanoKey using the web console
  * Answer: If you are using Windows, when a CanoKey just plugged into the system, Windows will try to read it for a short while after installing the driver. So just try again after a while.
  * Answer: If you are using Linux, some Linux program might try to use CanoKey for authencation. We suggest you to unplug your CanoKey and plug it again, then try connect again.
