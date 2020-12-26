+++
title = "NDEF"
date =  2020-12-27T02:30:15+08:00
weight = 5
+++

## Default values

One can learn from the [source code](https://github.com/canokeys/canokey-core/blob/master/applets/ndef/ndef.c) that

* Mode: default Read/Write, can be toggled in [Webconsole](https://console.canokeys.org/) Admin Applet
* Content: default NDEF message url "canokeys.org"
* Max NDEF Message Length: 1022Byte

## Usage

Often used in NFC, one may have many NDEF records into it. One may use it for name card exchanging or public key exchanging. Note that secrets should not be stored in NDEF as there is no encryption.

One may use `NFC Tools` to manipulate the NDEF message.

It is expected to be able to use some NDEF utils in webconsole, however there is no development.
