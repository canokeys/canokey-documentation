+++
title = "系统配置"
date =  2020-07-04T15:16:45+08:00
weight = 5
+++

本节介绍了使用 CanoKey 所需的必要设置。

## 1. Windows

无需配置即可使用。

## 2. macOS

### 2.1 Big Sur 及之前

请编译安装最新版本的 [ccid 驱动程序](https://ccid.apdu.fr/)。

### 2.2 Monterey、Ventura

无需配置即可使用。

### 2.3 Sonoma 及之后

CanoKey Pigeon 用户需编译安装最新版本的 [ccid 驱动程序](https://ccid.apdu.fr/)。

CanoKey Canary 用户无需配置即可使用。

### 2.4 pcsc-tools

pcsc-tools 提供 `pcsc_scan` 命令以便快速排查 PC/SC 驱动问题。

```sh
brew install pcsc-lite
```

## 3. Linux

Linux 用户可以执行如下配置，以更方便使用。

### 3.1 udev

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

`TAG+="uaccess"` 更偏向于 systemd，而 `GROUP="plugdev", MODE="0660"` 更偏传统。你可以选择任意一种解决方案。

添加此文件后，运行以下命令以应用更改。

```
udevadm control --reload-rules && udevadm trigger
```

### 3.2 CCID

请使用 ccid 1.4.34 或更新的版本。

### 3.3 pcsc-tools

pcsc-tools 提供 `pcsc_scan` 命令以便快速排查 PC/SC 驱动问题。