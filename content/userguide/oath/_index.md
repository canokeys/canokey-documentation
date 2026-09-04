+++
title = "OTP"
date = 2022-01-03T18:59:12+08:00
weight = 35
+++

OATH is [an organization](https://openauthentication.org/) that provides open authentication standards, including Time-based One-Time Password (TOTP) and HMAC-based One-Time Password (HOTP). These standards are commonly used to generate one-time codes for two-factor authentication.

Canary, Pigeon, and Epoxy all implement HOTP and TOTP.

## Storage Capacity

Firmware version 3.0.x and earlier can store up to 100 OATH credentials. From firmware version 3.1.1, there is no fixed credential limit. The actual number of credentials depends on available device storage, and no more credentials can be added when the storage is full.

## In This Chapter

* [Credentials](credentials/) — credential types, algorithms, the touch-required property, and the `otpauth://` URI format.
* [Managing Credentials](usage/) — adding, listing, calculating, and deleting credentials with CanoKey Console or `ckman`.
* [Challenge-Response](challenge-response/) — the two HMAC-SHA1 challenge-response slots for applications such as KeePassXC.
* [HOTP on Touch](hotp-on-touch/) — configuring CanoKey to type an HOTP code when touched.
