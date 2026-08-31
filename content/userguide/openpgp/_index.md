+++
title = "OpenPGP"
date = 2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/) is a signature and encryption standard specified by [RFC4880](https://tools.ietf.org/html/rfc4880). This standard achieves information and file signing/encryption through the use of private keys. One of the commonly used OpenPGP tools is GNU Privacy Guard, often abbreviated as GnuPG or GPG. In Windows, you can also use [Kleopatra](https://www.openpgp.org/software/kleopatra/).

CanoKey implements the OpenPGP Card specification version 3.4, holding up to three keys: a signature key (SIG), an encryption key (DEC), and an authentication key (AUT).

## Supported Algorithms

* RSA2048
* RSA3072
* RSA4096
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* secp256k1
* SM2

{{% notice note %}}
Firmware versions 1.6.1 and earlier only support RSA public keys with e = 65537.
Firmware version 2.0.0 and later support RSA3072 / RSA4096 key generation.
{{% /notice %}}

## In This Chapter

* [PIN and Touch Policies](pin-and-touch-policies/) — default values, PIN and touch policy semantics, and firmware-specific configuration.
* [Common Operations](usage/) — everyday GnuPG workflows: inspecting the card, generating or importing keys, and changing PINs.
* [FAQs](faq/) — troubleshooting GnuPG and PC/SC conflicts.
