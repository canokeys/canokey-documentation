+++
title = "PIV"
date =  2020-07-11T22:33:15+08:00
weight = 25
+++

Personal Identity Verification (PIV) is a US government standard defined as [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf). PIV uses a smartcard to store the key for signing / encryption. PIV is mainly used in non-web scenarios.

## Supported algorithm

* RSA2048
* NIST P-256
* NIST P-384

## Default values

* PIN: default 123456
* PUK: default 12345678
* Management Key: default 010203040506070801020304050607080102030405060708
