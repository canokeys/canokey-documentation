+++
title = "Key Slots"
date = 2020-07-11T22:33:15+08:00
weight = 1
+++

The PIV standard specifies 25 slots. Early CanoKey firmware supports only 5 of them; firmware version 2.0.0 adds two retired slots, and firmware version 3.1.1 supports all 25 slots plus a non-standard attestation slot. The table below lists each slot by number and name, along with the firmware version in which it was first supported.

Each slot has a name and a number. The slot number is a hex value, usually written without the "0x" prefix — for example, slot 9A. Some applications refer to a slot by its name instead: slot 9A is the "PIV Authentication" slot, so you may see phrases such as "the key in the Authentication slot".

| Slot number | Name | Firmware version first offered | Description |
|:------------|:-----|:-------------------------------|:------------|
| 80 | PIN | 2.0.0 | Not a standard slot; used by the Get Metadata command |
| 81 | PUK | 2.0.0 | Not a standard slot; used by the Get Metadata command |
| 9B | Management | all | Triple-DES key, or AES-192 beginning with firmware 3.1.1; no certificate |
| 9A | PIV Authentication | all | Asymmetric key and certificate; authenticates the user, usually for system login |
| 9C | Digital Signature | all | Asymmetric key and certificate; signing emails, files, executables, etc. |
| 9D | Key Management | all | Asymmetric key and certificate; encryption for confidentiality, e.g. decrypting emails |
| 9E | Card Authentication | all | Asymmetric key and certificate; authenticates the card, usually for building access |
| F9 | Attestation | 3.1.1 | Not a standard slot; P-256 key and certificate; attests other PIV keys generated on the device |
| 82 | Retired 1 | 2.0.0 | Asymmetric key and certificate; usually keys with expired certificates, used to decrypt older encrypted items |
| 83 | Retired 2 | 2.0.0 | Same as above |
| 84–94 | ... | 3.1.1 | ... |
| 95 | Retired 20 | 3.1.1 | Same as above |

Note that slot 9B holds a symmetric key, while all other slots hold asymmetric keys. Retired slots and their certificate objects are created only when used and consume available device storage.

## Attestation Key

The attestation key in slot F9 creates an attestation statement — an X.509 certificate — attesting that a key in slot 9A, 9C, 9D, 9E, or one of the retired slots (82–95) was generated on the device. Imported keys cannot be attested.

The attestation key is a P-256 key in slot F9, and its certificate is stored in data object `5FFF01`. A PIV application reset preserves both items. See [Attestation](attestation/) for details.

## Generate and Import Asymmetric Keys

Slots 9A, 9C, 9D, 9E, and 82–95 are manufactured empty. To fill them with keys, either generate a new key pair on the device or import an existing key; see [Common Operations](operations/).

## Signing

Slot 9C is the "Digital Signature" slot, and you will likely use it to sign emails, git commits, or other items. Signing is also possible with the keys in slots 9A, 9D, 9E, and 82–95. Slot 9B holds a symmetric key and cannot sign; slot F9 signs only attestation statements.
