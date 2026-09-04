---
title: "Protocols"
date: 2019-11-28T10:18:22-05:00
weight: 10
---

CanoKey supports the following protocols:

- FIDO2
- OpenPGP Smart Card 3.4
- PIV (NIST SP 800-73-4)
- OATH
- NDEF
- WebUSB

Besides, CanoKey also provides an additional admin applet to manage the key.

#### FIDO2

The implementations are following [CTAP2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-errata-20220621.html) and [CTAP2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html).

Supported features:

- Up to 64 resident keys
- The HMAC extension
- Ed25519

See the [FIDO2 / CTAP2 protocol documentation](ctap2/) for the command list, GetInfo contents, supported algorithms and extensions, and firmware-specific behavior.

#### OpenPGP Smart Card 3.4

CanoKey implements all the mandatory features of the [specification](https://gnupg.org/ftp/specs/OpenPGP-smart-card-application-3.4.pdf). Besides, the following optional features are also implemented:

- PUT DATA with TAG `C4`
- Algorithms
  - RSA 2048 (generate on card / import) / 4096 (import only)
  - ECDSA and ECDH: secp256r1 (NIST P256) / secp384r1 (NIST P384) / secp256k1
  - Ed25519 and X25519

Note that the following features are not supported:

- KDF
- Secure Messaging
- AES
- Command: MANAGE SECURITY ENVIRONMENT

See the [OpenPGP applet protocol documentation](openpgp/) for the command list, data objects, supported algorithms, and firmware-specific behavior.

#### PIV

CanoKey implements the mandatory features of the [specification](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf), including biometric and Security Object data objects. It also provides configurable algorithm IDs, PIV attestation, complete Retired Key Management slots, and other extensions.

See the [PIV applet protocol documentation](piv/) for the command list, data objects, supported algorithms, and firmware-specific extensions.

Secure Messaging is not supported.

#### OATH

Please refer to the [OATH documentation](oath/).

#### Admin Applet

Please refer to the [Admin Applet documentation](admin/).

#### NDEF

[NFC Forum Type-4 Tag](http://apps4android.org/nfc-specifications/NFCForum-TS-Type-4-Tag_2.0.pdf).

The maximum capacity of NDEF message is 1022-bytes.

See the [NDEF applet protocol documentation](ndef/) for the file layout, commands, and the read-only flag.

#### WebUSB

Please refer to the [WebUSB documentation](webusb/).
