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
```
After add this file, run the follow commands to apply changes.

```
udevadm control --reload-rules && udevadm trigger
```
