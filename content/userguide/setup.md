+++
title = "Setup"
date =  2020-07-04T15:16:45+08:00
weight = 5
+++

This section introduces the necessary settings required to use CanoKey.

## Windows

It can be used without configuration.

## macOS

### Big Sur and earlier

Please compile and install the latest version of the [ccid driver](https://ccid.apdu.fr/).

### Monterey, Ventura

It can be used without configuration.

### Sonoma and later

CanoKey Pigeon users need to compile and install the latest version of the [ccid driver](https://ccid.apdu.fr/).

CanoKey Canary users can use it without configuration.

### pcsc-tools

pcsc-tools provides the `pcsc_scan` command for quickly troubleshooting PC/SC driver issues.

## Linux

Linux users can perform the following configuration for easier use.

### udev

The aim of udev rules are to allow users without root privileges to use it. Please create `/etc/udev/rules.d/69-canokeys.rules` and fill in the following content.

```
# GnuPG/pcsclite
SUBSYSTEM!="usb", GOTO="canokeys_rules_end"
ACTION!="add|change", GOTO="canokeys_rules_end"
ATTRS{idVendor}=="20a0", ATTRS{idProduct}=="42d4", ENV{ID_SMARTCARD_READER}="1"
LABEL="canokeys_rules_end"

# FIDO2/U2F
# note that if you find this line in 70-u2f.rules, you can ignore it
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="20a0", ATTRS{idProduct}=="42d4", TAG+="uaccess", GROUP="plugdev", MODE="0660"

# make this usb device accessible for users, used in WebUSB
# change the mode so unprivileged users can access it, insecure rule, though
SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", MODE:="0666"
# if the above works for WebUSB (web console), you may change into a more secure way
# choose one of the following rules
# note if you use "plugdev", make sure you have this group and the wanted user is in that group
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", GROUP="plugdev", MODE="0660"
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", TAG+="uaccess"
```

`TAG+="uaccess"` is more inclined to systemd, while `GROUP="plugdev", MODE="0660"` is more traditional. You can choose either solution.

After adding this file, run the following command to apply the changes.

```
udevadm control --reload-rules && udevadm trigger
```

### CCID

Please use ccid version 1.4.34 or later.

### pcsc-tools

pcsc-tools provides the `pcsc_scan` command for quickly troubleshooting PC/SC driver issues.