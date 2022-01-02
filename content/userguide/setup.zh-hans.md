+++
title = "基本设置"
date =  2020-07-04T15:16:45+08:00
weight = 5
+++

本节介绍了使用 CanoKey 所需的必要设置。

## Windows

通常，Windows 用户无需任何配置即可使用。

## macOS

使用 macOS Monterey 及更新版本的用户不需要额外配置。如您使用旧版 macOS，需要手动配置 CCID 白名单。方法如下：

待确认

## Linux

Linux 用户可以执行如下配置，以更方便使用。

### udev

`udev` 规则的增加是为了在非 root 用户下使用，请创建 `/etc/udev/rules.d/69-canokeys.rules` 并填入以下内容。

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

`TAG+="uaccess"` is more systemd related while `GROUP="plugdev", MODE="0660"` is more traditional. You can choose either solution of them.

After adding this file, run the follow commands to apply changes.

```
udevadm control --reload-rules && udevadm trigger
```

### CCID

使用 ccid 1.4.34 或更新的版本即可。

### libfido2

`libfido2` is for FIDO2/U2F related programs. Other dependencies may be checked by guides from Yubico.
