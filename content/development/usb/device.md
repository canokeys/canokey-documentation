---
title: "Device"
date: 2019-11-28T10:18:29-05:00
weight: 10
---

CanoKey is a full-speed USB device.

The USB version is set to 2.1 in order to make the host to request a [BOS descriptor](https://techcommunity.microsoft.com/t5/Microsoft-USB-Blog/USB-2-1-2-0-1-1-device-enumeration-changes-in-Windows-8/ba-p/270775). In the BOS descriptor, the CanoKey declares two capabilities:

- WebUSB
- Microsoft OS 2.0

The Microsoft OS 2.0 Platform Capability Descriptor is used to declare a "WinUSB" device so that no additional driver is required.
