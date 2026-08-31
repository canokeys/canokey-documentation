---
title: "FIDO2 / CTAP2"
date: 2026-08-21T00:00:00+08:00
weight: 5
---

The CanoKey FIDO2 applet implements the Client to Authenticator Protocol (CTAP). Depending on the firmware version it conforms to [CTAP 2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) or [CTAP 2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-errata-20220621.html), and it also implements the U2F (CTAP1) protocol. This page documents the supported commands, extensions, algorithms, and firmware-specific behavior.

## 1. Transports and Version Support

The applet is available over USB (CTAPHID) and NFC. On the ISO 7816 interface (USB CCID or NFC), select the FIDO application with AID `A0 00 00 06 47 2F 00 01`; CTAP2 commands are then sent as CBOR messages in APDUs with `CLA = 80, INS = 10`, and U2F commands use `CLA = 00` (see Section 5).

| CTAP version | Firmware requirement |
|:-------------|:---------------------|
| CTAP 2.0 (`FIDO_2_0`) | All firmware versions |
| CTAP 2.1 (`FIDO_2_1`) | 2.0.0+ |

`U2F_V2` is also reported in the versions list on firmware versions that support U2F (see Section 5).

## 2. Commands

| Command | Code | Availability |
|:--------|:----:|:-------------|
| authenticatorMakeCredential | `01` | All firmware versions |
| authenticatorGetAssertion | `02` | All firmware versions |
| authenticatorGetInfo | `04` | All firmware versions |
| authenticatorClientPIN | `06` | All firmware versions |
| authenticatorReset | `07` | All firmware versions |
| authenticatorGetNextAssertion | `08` | All firmware versions |
| authenticatorCredentialManagement | `0A` | 2.0.0+ (`41` accepted as a legacy alias) |
| authenticatorSelection | `0B` | All firmware versions |
| authenticatorLargeBlobs | `0C` | 2.0.0+ |

authenticatorCredentialManagement supports the sub-commands getCredsMetadata, enumerateRPsBegin, enumerateRPsGetNextRP, enumerateCredentialsBegin, enumerateCredentialsGetNextCredential, deleteCredential, and updateUserInformation.

authenticatorReset is only accepted within 10 seconds after power-up and requires user presence (a touch).

## 3. authenticatorGetInfo

Firmware version 2.0.0 and later report the following fields. Items marked "dynamic" depend on the device state.

| Key | Field | Value |
|:---:|:------|:------|
| `01` | versions | `U2F_V2` (if supported), `FIDO_2_0`, `FIDO_2_1` |
| `02` | extensions | `credBlob`, `credProtect`, `hmac-secret`, `largeBlobKey` |
| `03` | aaguid | `244eb29e-e090-4e49-81fe-1f20f8d3b8f4` |
| `04` | options | `rk`, `credMgmt`, `largeBlobs`, `pinUvAuthToken`: true; `clientPin`, `makeCredUvNotRqd`: dynamic |
| `05` | maxMsgSize | Device-dependent |
| `06` | pinUvAuthProtocols | `[1, 2]` |
| `07` | maxCredentialCountInList | 16 |
| `08` | maxCredentialIDLength | 70 |
| `09` | transports | `nfc`, `usb` |
| `0A` | algorithms | See Section 4 |
| `0B` | maxSerializedLargeBlobArray | 4096 |
| `0F` | maxCredBlobLength | 32 |
| `16` | attestationFormats | `packed` |

Earlier firmware versions report a subset: firmware 1.x lists versions `FIDO_2_0` and `U2F_V2`, the `hmac-secret` extension, PIN protocol 1, and the `rk` and `clientPin` options. Firmware 2.0.0 adds `FIDO_2_1`, PIN protocol 2, the `credProtect`, `credBlob`, and `largeBlobKey` extensions, and the `credMgmt`, `largeBlobs`, `pinUvAuthToken`, and `makeCredUvNotRqd` options.

## 4. Algorithms

| Algorithm | COSE alg | Availability |
|:----------|:--------:|:-------------|
| ES256 (ECDSA over NIST P-256 with SHA-256) | `-7` | All firmware versions |
| EdDSA (Ed25519) | `-8` | All firmware versions |
| SM2 | `-48` | 3.0.0+ |

The SM2 algorithm and curve identifiers are vendor-configurable through the Admin applet (instructions `11h` / `12h`). On firmware 3.0.x SM2 is disabled by default and only appears in the algorithms list after it has been enabled; the default algorithm ID is `-48`.

## 5. U2F (CTAP1)

The U2F commands use `CLA = 00`: Register (`INS = 01`), Authenticate (`INS = 02`, `P1 = 03` to enforce user presence or `P1 = 07` for check-only), and Version (`INS = 03`, returns `U2F_V2`). U2F credentials are ES256 key handles compatible with CTAP2 non-discoverable credentials.

Firmware version 3.0.0 does not support U2F; firmware 3.0.2 and later restore it.

## 6. Discoverable Credentials

Discoverable credentials (resident keys) are supported on all firmware versions. CanoKey can store up to 64 discoverable credentials.

## 7. PIN

No PIN is set by default. PIN protocols 1 and 2 are supported (protocol 2 from firmware 2.0.0). The PIN retry counter is 8. authenticatorClientPIN supports the sub-commands defined by the implemented CTAP versions, including permission-based pinUvAuthTokens (`getPinUvAuthTokenUsingPinWithPermissions`) from firmware 2.0.0.

Attestation uses the `packed` format with the device key and certificate; the attestation key and certificate can be provisioned through the Admin applet (see the [Admin Applet documentation](../admin/)).
