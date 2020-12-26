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
* X25519
* Ed25519
* NIST P-256 (secp256r1)
* NIST P-384 (secp384r1)
* secp256k1

\* Due to computing performance with STM32, RSA4096 cannot be generated in the card. However you can generate the key pair and import it to CanoKey.

Note that RSA3072 is not supported currently.

It is worth mentioning that one may refer to [this blog on multiple ecc curves](https://jia.je/crypto/2020/05/21/ecc-curves/) on aliases of one curve.

## Using GnuPG

Please refer to the [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html) at the moment.
