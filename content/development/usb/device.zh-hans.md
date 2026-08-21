---
title: "设备"
date: 2019-11-28T10:18:29-05:00
weight: 10
---

CanoKey 是一个全速（full-speed）USB 设备。

USB 版本号被设置为 2.1，以使主机请求 [BOS 描述符](https://techcommunity.microsoft.com/t5/Microsoft-USB-Blog/USB-2-1-2-0-1-1-device-enumeration-changes-in-Windows-8/ba-p/270775)。在 BOS 描述符中，CanoKey 声明了两个能力：

- WebUSB
- Microsoft OS 2.0

Microsoft OS 2.0 平台能力描述符（Platform Capability Descriptor）用于将设备声明为 "WinUSB" 设备，从而无需安装额外的驱动程序。
