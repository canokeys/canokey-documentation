+++
title = "Key Slots"
date = 2020-07-11T22:33:15+08:00
weight = 1
+++

The PIV standard specifies 25 slots. CanoKey supports the primary PIV slots and, from firmware version 2.0.0, two Retired Key Management slots. The table below lists each supported slot by number and name, along with the firmware version in which it was first supported.

Each slot has a name and a number. The slot number is a hex value, usually written without the "0x" prefix — for example, slot 9A. Some applications refer to a slot by its name instead: slot 9A is the "PIV Authentication" slot, so you may see phrases such as "the key in the Authentication slot".

| Slot number | Name | Firmware version first offered | Description |
|:------------|:-----|:-------------------------------|:------------|
| 80 | PIN | 2.0.0 | Not a standard slot; used by the Get Metadata command |
| 81 | PUK | 2.0.0 | Not a standard slot; used by the Get Metadata command |
| 9B | Management | all | Triple-DES key; no certificate |
| 9A | PIV Authentication | all | Asymmetric key and certificate; authenticates the user, usually for system login |
| 9C | Digital Signature | all | Asymmetric key and certificate; signing emails, files, executables, etc. |
| 9D | Key Management | all | Asymmetric key and certificate; encryption for confidentiality, e.g. decrypting emails |
| 9E | Card Authentication | all | Asymmetric key and certificate; authenticates the card, usually for building access |
| 82 | Retired 1 | 2.0.0 | Asymmetric key and certificate; usually keys with expired certificates, used to decrypt older encrypted items |
| 83 | Retired 2 | 2.0.0 | Same as above |

Note that slot 9B holds a symmetric key, while all other slots hold asymmetric keys.

## Generate and Import Asymmetric Keys

Slots 9A, 9C, 9D, 9E, 82, and 83 are manufactured empty. To fill them with keys, either generate a new key pair on the device or import an existing key; see [Common Operations](operations/).

## Signing

Slot 9C is the "Digital Signature" slot, and you will likely use it to sign emails, git commits, or other items. Signing is also possible with the keys in slots 9A, 9D, 9E, 82, and 83. Slot 9B holds a symmetric key and cannot sign.
