+++
title = "NDEF"
date =  2020-12-27T02:30:15+08:00
weight = 30
+++

NDEF (NFC Data Exchange Format) is a lightweight, extensible binary data format that typically contains data record formats such as URLs and text.

## Default Values

* Mode: Default is read/write mode, which can be modified in the console
* Content: Default is URL, with value "https://canokeys.org"
* Maximum length: 1022 bytes

## How to Use

1. Download and install the NFC Tools application.
2. Open the NFC Tools application, select the "Read" option to read existing NDEF tag content.
3. Select the "Write" option to write new content to the NDEF tag. Note that NDEF tags can be rewritten.
4. Bring the top of your iPhone or the back of your Android device close to the CanoKey to complete the read/write operation.

Please note that the NDEF function has no encryption protection, so the transmitted information is stored and transmitted in plain text. Please use it cautiously according to your security requirements and avoid storing and transmitting sensitive information.
