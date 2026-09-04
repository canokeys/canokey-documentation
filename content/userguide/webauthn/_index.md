+++
title = "WebAuthn (Passkey)"
date = 2021-01-16T01:30:15+08:00
weight = 15
+++

CanoKey supports the WebAuthn (passkey) authentication protocol through the CTAP specification.

## CTAP Version Support

| CTAP Version | Firmware Requirement |
| --- | --- |
| CTAP 2.0 | All versions |
| CTAP 2.1 | Firmware 2.0.0 and later |
| CTAP 2.3 | Firmware 3.1.1 and later |

## Features

Supported features include:

- Discoverable Credentials (Resident Keys)
- HMAC extensions
- Ed25519 algorithm

Firmware version 2.0.0 adds:

- Discoverable Credentials management
- PIN Protocol 2
- `credProtect`, `credBlob`, and `largeBlobKey` extensions
- Large Blob

Firmware version 3.0.0 adds:

- SM2 algorithm

Firmware version 3.1.1 adds:

- Configuration options including `alwaysUv`, minimum PIN length, forced PIN change, and long-press reset settings
- `minPinLength`, `thirdPartyPayment`, and HMAC-secret during credential creation (`hmac-secret-mc`) extensions
- ML-DSA-65 (`alg = -49`) credentials; the default algorithm ID for SM2 changes from `-48` to `-54`
- No fixed limit of 64 Discoverable Credentials: actual capacity depends on available device storage, and the device also reports the remaining capacity
- U2F support when `alwaysUv` is disabled

{{% notice note %}}
Firmware version 3.0.0 does not support U2F, WebAuthn over USB on iOS 17.4 and 18, or WebAuthn on macOS, including Safari, Firefox, and applications that rely on Apple's CTAP stack. Firmware version 3.0.2 and later are not affected by these limitations.
{{% /notice %}}

## Multi-Factor Authentication

CanoKey can be used for two-factor authentication on many [websites](https://2fa.directory/int/).

{{% notice note %}}
By default, CanoKey does not set a PIN. Some websites and certain features (such as Discoverable Credentials management) require you to set a PIN. Please set it when prompted.
{{% /notice %}}

## In This Chapter

* [PIN and Configuration](pin-and-config/) — PIN setup, PIN Protocol 2, and firmware 3.1.1 configuration options.
* [Credentials](credentials/) — Discoverable Credentials, credential management, and related extensions.
* [Reset](reset/) — resetting the WebAuthn application and its consequences.
* [SSH](ssh/) — using FIDO keys (`ecdsa-sk` / `ed25519-sk`) for OpenSSH authentication.
* [PAM](pam/) — PAM authentication with pam-u2f.
* [HMAC-secret Extension](hmac-secret/) — LUKS full-disk encryption with systemd-cryptenroll.
