---
title: "PIV Applet"
date: 2026-08-18T00:00:00+08:00
weight: 25
---

The CanoKey PIV applet implements the mandatory features of [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf) and a set of extensions. This page documents CanoKey-specific behavior and the commands exposed by the implementation. Refer to NIST SP 800-73-4 for the standard command and TLV definitions.

## 1. Application and Transport

### 1.1 AID

The PIV application identifier is:

```text
A0 00 00 03 08 00 00 10 00 01 00
```

Select it with `00 A4 04 00 0B A000000308000010000100`.

### 1.2 APDU Transport

CanoKey accepts short APDUs. Extended-length command APDUs are rejected with `6700`.

When response data does not fit in one response, CanoKey returns `61xx`. Use Get Response (`INS = C0`) to retrieve the remaining data.

### 1.3 Instructions

| INS | Name | Definition |
|:---:|:-----|:-----------|
| `20` | Verify | NIST SP 800-73-4 |
| `24` | Change Reference Data | NIST SP 800-73-4 |
| `2C` | Reset Retry Counter | NIST SP 800-73-4 |
| `47` | Generate Asymmetric Key Pair | NIST SP 800-73-4 |
| `87` | General Authenticate | NIST SP 800-73-4 |
| `A4` | Select | ISO 7816-4 |
| `C0` | Get Response | ISO 7816-4 |
| `CB` | Get Data | NIST SP 800-73-4 |
| `DB` | Put Data | NIST SP 800-73-4 |
| `EE` | Algorithm Extension | CanoKey extension |
| `F7` | Get Metadata | Yubico-compatible extension |
| `F8` | Get Serial | Yubico-compatible extension |
| `FB` | Reset | Yubico-compatible extension |
| `FD` | Get Version | Yubico-compatible extension |
| `FE` | Import Asymmetric Key | Yubico-compatible extension |
| `FF` | Set Management Key | Yubico-compatible extension |

## 2. Algorithms

### 2.1 Algorithm IDs

The standard algorithm IDs are fixed. IDs for the extended algorithms are configurable and must be read from the device when interoperability depends on their values.

| Algorithm | Default ID | Availability |
|:----------|:----------:|:-------------|
| RSA 2048 | `07` | All supported firmware versions |
| NIST P-256 | `11` | All supported firmware versions |
| NIST P-384 | `14` | All supported firmware versions |
| RSA 3072 | `05` | 3.0.0+ |
| RSA 4096 | `16` | 3.0.0+ |
| Ed25519 | `E0` | 3.0.0+ |
| X25519 | `E1` | 3.0.0+ |
| secp256k1 | `53` | 3.0.0+ |
| SM2 | `54` | 3.0.0+ |

Firmware version 3.0.0 accepts only 32-byte input for Ed25519 signing and only internally generated X25519 keys. Firmware version 3.0.2 and later remove these restrictions.

### 2.2 Algorithm Extension Command

The Algorithm Extension command reads or writes the seven-byte extended-algorithm configuration record.

| Operation | APDU header | Authentication | Data |
|:----------|:------------|:---------------|:-----|
| Read | `00 EE 01 00` | None | Empty |
| Write | `00 EE 02 00` | Management key | Seven-byte record |

The record fields are ordered as follows:

| Offset | Field | Default |
|:------:|:------|:-------:|
| 0 | Extended algorithms enabled | `01` |
| 1 | Ed25519 ID | `E0` |
| 2 | RSA 3072 ID | `05` |
| 3 | RSA 4096 ID | `16` |
| 4 | X25519 ID | `E1` |
| 5 | secp256k1 ID | `53` |
| 6 | SM2 ID | `54` |

The enable field must be `00` or `01`. Algorithm IDs can use any byte value and may overlap. A successful write takes effect immediately.

## 3. Key Slots and Policies

| Slot | Purpose | Default PIN policy | Default touch policy |
|:----:|:--------|:-------------------|:---------------------|
| `9A` | PIV Authentication | Once | Never |
| `9C` | Digital Signature | Once | Never |
| `9D` | Key Management | Once | Never |
| `9E` | Card Authentication | Never | Never |
| `82`-`83` | Retired Key Management | Once | Never |

Firmware version 2.0.0 and later support Retired Key Management slots `82` and `83`.

PIN policies use `Never`, `Once`, and `Always`. Touch policies use `Never`, `Always`, and `Cached`; the cached interval is 15 seconds. Touch requirements apply only over USB and are not enforced over NFC.

## 4. Data Objects

Get Data and Put Data use the standard `5C` tag list and `53` data container defined by NIST SP 800-73-4. All writable objects require management-key authentication.

| Tag | Data object | Capacity |
|:---:|:------------|---------:|
| `7E` | Discovery Object | Synthesized |
| `5FC101` | Card Authentication Certificate | 3000 bytes |
| `5FC102` | Cardholder Unique Identifier | 2916 bytes |
| `5FC105` | PIV Authentication Certificate | 3000 bytes |
| `5FC107` | Card Capability Container | 287 bytes |
| `5FC109` | Printed Information | 245 bytes |
| `5FC10A` | Digital Signature Certificate | 3000 bytes |
| `5FC10B` | Key Management Certificate | 3000 bytes |
| `5FC10D`-`5FC10E` | Retired Key Management Certificates | 3000 bytes each |
Certificate capacities are 3000 bytes on firmware version 1.6 and later and 1000 bytes on firmware version 1.5 and earlier.

## 5. Other Extension Commands

### 5.1 Get Metadata

Use `00 F7 00 <reference>` with no data. The reference can be PIN (`80`), PUK (`81`), management key (`9B`), or a supported asymmetric-key slot. The response follows the Yubico PIV metadata TLV format and reports values such as algorithm, policies, origin, public key, default status, and retry counters where applicable.

### 5.2 Get Serial and Version

- `00 F8 00 00` returns the four-byte device serial number.
- `00 FD 00 00` returns the three-byte PIV applet version.

### 5.3 Import Asymmetric Key

Use `INS = FE`, with the algorithm ID in `P1` and the destination slot in `P2`. Management-key authentication is required. The imported key format follows the Yubico PIV TLV format.

### 5.4 Reset

Use `00 FB 00 00` with no data. Reset is accepted only when both PIN and PUK are blocked. It restores PIV user data and defaults.
