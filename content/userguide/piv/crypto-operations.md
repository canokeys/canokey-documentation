+++
title = "Cryptographic Operations"
date = 2020-07-11T22:33:15+08:00
weight = 7
+++

The asymmetric key slots are intended for different kinds of cryptographic operations:

| Slot | Intended use |
|:-----|:-------------|
| 9A (PIV Authentication) | Authenticating the user, usually for system login |
| 9C (Digital Signature) | Signing emails, files, executables, git commits, etc. |
| 9D (Key Management) | Encryption for confidentiality, e.g. decrypting emails |
| 9E (Card Authentication) | Authenticating the card, usually for building access |
| 82–83 (Retired Key Management) | Decrypting older data encrypted to keys with expired certificates |

Signing is not restricted to slot 9C: the keys in slots 9A, 9D, 9E, 82, and 83 can sign as well. Slot 9B holds a symmetric key and cannot sign.

Slot 9D is used for decryption with RSA keys and for ECDH key agreement with EC keys.

ECDSA signatures are returned in ASN.1 DER encoding, as required by the PIV standard.

## PIN and Touch Policy Interplay

Whether an operation prompts for the PIN, a touch, or both is governed by the [PIN and touch policies](pin-touch-policies/) configured for the slot. For example, with the default policies, slot 9E does not require PIN verification, while slot 9A requires it once per session. Touch requirements apply only over USB and are not enforced over NFC. The policies in effect on a device can be read from its [metadata](metadata/).
