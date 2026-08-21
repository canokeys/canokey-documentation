+++
title = "Credentials"
date = 2022-01-03T18:59:12+08:00
weight = 1
+++

An OATH credential on a CanoKey consists of a name (typically `issuer:account`), a secret key, and a set of parameters that define how one-time codes are generated.

## Credential Types

| Type | Description |
| ---- | ----------- |
| HOTP | HMAC-based One-Time Password. Codes are generated from an internal counter that advances each time a code is calculated. |
| TOTP | Time-based One-Time Password. Codes are generated from the current time, using a configurable time period (commonly 30 seconds). |

## Algorithms

| Algorithm   | Description |
| ----------- | ----------- |
| HMAC-SHA1   | The most widely supported algorithm; the default in most services. |
| HMAC-SHA256 | A stronger alternative supported by some services. |

## Number of Digits

The number of digits in the generated code is configurable per credential. Six digits is the most common choice.

## Touch Requirement

A credential can carry the touch-required property. When set, generating a code for that credential requires physically touching the CanoKey, which confirms user presence for every code calculation.

## The `otpauth://` URI Format

Many services provide OATH credentials as an `otpauth://` URI, either displayed as text or encoded in a QR code. The format is:

```
otpauth://TYPE/LABEL?secret=SECRET&issuer=ISSUER&algorithm=ALGORITHM&digits=DIGITS&period=PERIOD
```

* `TYPE` is `hotp` or `totp`.
* `LABEL` identifies the credential, usually in the form `issuer:account`.
* `secret` is the shared secret key, Base32-encoded.
* `issuer`, `algorithm`, `digits`, and `period` specify the corresponding credential parameters.

An `otpauth://` URI can be imported directly with `ckman`; see [Managing Credentials](usage/).

{{% notice note %}}
Unlike Yubico's OATH implementation, CanoKey does not support password-protected access to the OATH applet: there is no access password and no unlock step. Anyone with access to the device can calculate codes for credentials that do not require touch.
{{% /notice %}}
