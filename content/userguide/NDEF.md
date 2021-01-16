+++
title = "NDEF"
date =  2020-12-27T02:30:15+08:00
weight = 30
+++

## Default values

* Mode: default Read/Write, can be toggled in [Web Console](https://console.canokeys.org/) by using Admin Applet
* Content: default NDEF message with the URL "https://canokeys.org"
* Max NDEF Message Length: 1022 Bytes

## Usage

You may use it as an NFC tag that contains a URL, a vCard, or anything that NDEF supports.
**Note that secrets should not be stored in NDEF as there is no encryption.**

You may use [NFC Tools](https://play.google.com/store/apps/details?id=com.wakdev.wdnfc) to manipulate the NDEF message.
