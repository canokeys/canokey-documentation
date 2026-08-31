+++
title = "Certificates"
date = 2020-07-11T22:33:15+08:00
weight = 4
+++

Each asymmetric key slot has a corresponding certificate object that holds an X.509 certificate for the key in that slot. Slot 9B holds a symmetric key and has no certificate. Certificate objects are readable without authentication; writing a certificate requires management-key authentication.

| Slot | Certificate object |
|:-----|:-------------------|
| 9A (PIV Authentication) | `5FC105` |
| 9C (Digital Signature) | `5FC10A` |
| 9D (Key Management) | `5FC10B` |
| 9E (Card Authentication) | `5FC101` |
| 82–83 (Retired Key Management) | `5FC10D`–`5FC10E` |

The maximum size of a certificate object depends on the firmware version:

* Firmware version 1.5 or earlier: 1000 bytes
* Firmware version 1.6 or later: 3000 bytes

## Provisioning a Certificate

The usual workflow is to generate a key pair on the device (or import an existing key), then obtain a certificate for the public key — either a self-signed certificate or one issued by a CA from a certificate signing request — and finally import the certificate into the slot's certificate object. For step-by-step commands, see [Common Operations](operations/).
