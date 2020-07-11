+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 5
+++

[OpenPGP](https://www.openpgp.org/) is a standard for signing and encrypting defined as [RFC4880](https://tools.ietf.org/html/rfc4880). It uses a private key to sign / encrypt messages and documents. One of the most commonly used tool for using OpenPGP is GNU Privacy Guard, which is short as GnuPG or GPG. So you might see some online materials also use GPG to denote the OpenPGP related techology.

The private key can be stored in CanoKey, or you can use CanoKey to generate a OpenPGP key. The private key stored in CanoKey cannot be read out. This reduced the chance of leakage of the private key.

## Supported algorithm

* RSA2048
* RSA4096\*
* Curve 25519
* NIST P-256
* NIST P-512
* secp256k1

\* Due to computing performance with STM32, RSA4096 cannot be generated in the card. However you can generate the key pair and import it to CanoKey.

## Using GnuPG

Please refer to the [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html) at the moment.
