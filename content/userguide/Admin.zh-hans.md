+++
title = "设置"
date =  2020-12-27T02:30:15+08:00
weight = 10
+++

## 1. 主要功能

CanoKey 的设置功能包含了管理和配置设备的重要操作。目前，您可以通过以下方式管理 CanoKey：

* Web 控制台（仅支持 Chrome 及 Chromium 内核浏览器）：<https://console.canokeys.org>
* [iOS App](https://apps.apple.com/app/canokey-console/id6476454147)
* [Android App](https://play.google.com/store/apps/details?id=org.canokeys.console)

CanoKey Pigeon 在 iOS App 中仅支持通过 NFC 访问。

设置功能的主要配置内容包括：

* LED 状态：默认启用
* NDEF（NFC Data Exchange Format）：默认启用
* WebUSB 登录页面：默认启用
* 重置

固件版本 3.1.1 起新增功能开关，可分别启用或停用 Password/HOTP 键盘输出、通过 USB 或 NFC 使用 OpenPGP 和 PIV，以及 WebAuthn。管理软件还可以查看设备存储空间的总用量和各应用用量、查看设备所用 `canokey-core` 的具体版本，以及安装包含 128 个条目的自定义键盘布局。

{{% notice note %}}
以上设置需要 3.1.1 或更高版本固件。界面中是否显示相应设置，还取决于 CanoKey Console 的版本。
{{% /notice %}}

## 2. 重置

### 2.1 单独重置应用

如果您知道 Admin PIN，您可以通过控制台独立重置各个应用。

**被重置应用的数据将会清空**

### 2.2 重置 CanoKey

如果您不记得 Admin PIN，您可以在 Admin PIN **完全锁死** 的情况下重置 CanoKey。
在控制台的设置应用中，点击“重置”，CanoKey 的 LED 指示灯会闪烁，闪烁时，请触摸密钥；重复上述操作，直到停止闪烁。

**所有用户应用数据将会清空。** 序列号、设备证明密钥等出厂写入的数据会保留。
