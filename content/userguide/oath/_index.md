+++
title = "OTP"
date = 2022-01-03T18:59:12+08:00
weight = 35
+++

OATH is [an organization](https://openauthentication.org/) that provides open authentication standards, including Time-based One-Time Password (TOTP) and HMAC-based One-Time Password (HOTP). These standards are commonly used to generate one-time codes for two-factor authentication.

Canary, Pigeon, and Epoxy all implement HOTP and TOTP.

## Storage Capacity

CanoKey can store up to 100 OATH credentials.

## In This Chapter

* [Credentials](credentials/) — credential types, algorithms, the touch-required property, and the `otpauth://` URI format.
* [Managing Credentials](usage/) — adding, listing, calculating, and deleting credentials with CanoKey Console or `ckman`.
* [HOTP on Touch](hotp-on-touch/) — configuring CanoKey to type an HOTP code when touched.
