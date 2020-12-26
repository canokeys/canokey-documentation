+++
title = "Setup"
date =  2020-07-04T15:16:45+08:00
weight = 5
+++

This section describes what you need to do before you get started using your CanoKey.

## Linux

In order to allow non-root user use the key, you need to add a udev rule into /usr/lib/udev/rules.d/69-canokeys.rules

```
SUBSYSTEM!="usb", GOTO="canokeys_rules_end"
ACTION!="add|change", GOTO="canokeys_rules_end"
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="0007", ENV{ID_SMARTCARD_READER}="1"
ATTRS{idVendor}=="16d0", ATTRS{idProduct}=="0e80", ENV{ID_SMARTCARD_READER}="1"
ATTRS{idVendor}=="20a0", ATTRS{idProduct}=="42d4", ENV{ID_SMARTCARD_READER}="1"
LABEL="canokeys_rules_end"

# make this usb device accessible for users, used in WebUSB
# change the mode so unprivileged users can access it, insecure rule, though
SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", MODE:="0666"
# if the above works for WebUSB (web console), you may change into a more secure way
# choose one of the following rules
# note if you use "plugdev", make sure you have this group and the wanted user is in that group
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", GROUP="plugdev", MODE="0660"
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", TAG+="uaccess"
```
After add this file, run the follow commands to apply changes.

```
udevadm control --reload-rules && udevadm trigger
```
