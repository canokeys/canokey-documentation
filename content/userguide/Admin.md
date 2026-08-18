+++
title = "Settings"
date =  2020-12-27T02:30:15+08:00
weight = 10
+++

## 1. Main Functions

The settings of CanoKey include important operations for managing and configuring the device. Currently, you can manage CanoKey through the following methods:

* Web Console (Supports only Chrome and Chromium-based browsers): <https://console.canokeys.org>
* [iOS App](https://apps.apple.com/app/canokey-console/id6476454147)
* [Android App](https://play.google.com/store/apps/details?id=org.canokeys.console)

CanoKey Pigeon only supports NFC access in the iOS App.

The main configuration contents of the settings include:

* LED Status: Enabled by default
* NDEF (NFC Data Exchange Format): Enabled by default
* WebUSB Login Page: Enabled by default
* Reset

Firmware version 3.1.1 adds separate switches for Password/HOTP keyboard output, OpenPGP and PIV over USB or NFC, and WebAuthn. Management software can also display total and per-application storage usage, show the exact `canokey-core` version used by the device, and install a custom keyboard layout containing 128 entries.

{{% notice note %}}
These settings require firmware version 3.1.1 or later. Whether they appear in the interface also depends on the CanoKey Console version.
{{% /notice %}}

## 2. Reset

### 2.1 Reset Application Individually

If you know the Admin PIN, you can reset each application individually through the console.

**The data of the reset application will be erased**

### 2.2 Reset CanoKey

If you do not remember the Admin PIN, you can reset CanoKey when the Admin PIN is completely locked out.
In the settings application of the console, click "Reset", and the LED indicator of CanoKey will flash. When it flashes, please touch the key; repeat the above operation until it stops flashing.

**All user application data will be erased.** Factory-provisioned data, such as the serial number and device attestation keys, is preserved.
