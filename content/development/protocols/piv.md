---
title: "PIV Applet"
date: 2026-08-18T00:00:00+08:00
weight: 25
---

The CanoKey PIV applet implements the mandatory features of [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf) and a set of extensions. This page documents CanoKey-specific behavior and the commands exposed by the implementation. Refer to NIST SP 800-73-4 for the standard command and TLV definitions.

Unless a section states otherwise, the extensions documented on this page require firmware version 3.1.1 or later.

## 1. Application and Transport

### 1.1 AID

The PIV application identifier is:

```text
A0 00 00 03 08 00 00 10 00 01 00
```

Select it with `00 A4 04 00 0B A000000308000010000100`.

### 1.2 APDU Transport

CanoKey accepts short APDUs. Extended-length command APDUs are rejected with `6700`.

ISO 7816-4 command chaining (`CLA = 10`) is supported for:

- General Authenticate (`INS = 87`)
- Put Data (`INS = DB`)
- Import Asymmetric Key (`INS = FE`)

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
| `F6` | Move/Delete Key | Yubico-compatible extension |
| `F7` | Get Metadata | Yubico-compatible extension |
| `F8` | Get Serial | Yubico-compatible extension |
| `F9` | Attest | Yubico-compatible extension |
| `FA` | Set PIN Retries | Yubico-compatible extension |
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
| NIST P-521 | `15` | 3.1.1+ |

Firmware version 3.0.0 accepts only 32-byte input for Ed25519 signing and only internally generated X25519 keys. Firmware version 3.0.2 and later remove these restrictions.

### 2.2 Algorithm Extension Command

The Algorithm Extension command reads or writes the eight-byte extended-algorithm configuration record.

| Operation | APDU header | Authentication | Data |
|:----------|:------------|:---------------|:-----|
| Read | `00 EE 01 00` | None | Empty |
| Write | `00 EE 02 00` | Management key | Eight-byte record |

The record fields are ordered as follows:

| Offset | Field | Default |
|:------:|:------|:-------:|
| 0 | Extended algorithms enabled | `01` |
| 1 | Ed25519 ID | `E0` |
| 2 | RSA 3072 ID | `05` |
| 3 | RSA 4096 ID | `16` |
| 4 | X25519 ID | `E1` |
| 5 | secp256k1 ID | `53` |
| 6 | NIST P-521 ID | `15` |
| 7 | SM2 ID | `54` |

The enable field must be `00` or `01`. Algorithm IDs can use any byte value and may overlap. A successful write takes effect immediately.

## 3. Key Slots and Policies

| Slot | Purpose | Default PIN policy | Default touch policy |
|:----:|:--------|:-------------------|:---------------------|
| `9A` | PIV Authentication | Once | Never |
| `9C` | Digital Signature | Always | Never |
| `9D` | Key Management | Once | Never |
| `9E` | Card Authentication | Never | Never |
| `82`-`95` | Retired Key Management | Once | Never |
| `F9` | Attestation | Not applicable | Not applicable |

Firmware version 2.0.0 supports Retired Key Management slots `82` and `83`. Firmware version 3.1.1 supports the complete range from `82` through `95`; storage for these slots is allocated only when used.

The attestation slot accepts only a P-256 key. Its key and certificate are provisioned separately and are preserved when the PIV application is reset.

PIN policies use `Never`, `Once`, and `Always`. Touch policies use `Never`, `Always`, and `Cached`; the cached interval is 15 seconds. Touch requirements apply only over USB and are not enforced over NFC.

## 4. Data Objects

Get Data and Put Data use the standard `5C` tag list and `53` data container defined by NIST SP 800-73-4. All writable objects require management-key authentication. Optional objects are allocated only after they are written.

| Tag | Data object | Capacity | Read access |
|:---:|:------------|---------:|:------------|
| `7E` | Discovery Object | Synthesized | Public, read-only |
| `7F61` | Biometric Information Templates Group Template | Synthesized | Public, read-only |
| `5FC101` | Card Authentication Certificate | 3000 bytes | Public |
| `5FC102` | Cardholder Unique Identifier | 2916 bytes | Public |
| `5FC103` | Cardholder Fingerprints | 512 bytes | PIN |
| `5FC105` | PIV Authentication Certificate | 3000 bytes | Public |
| `5FC106` | Security Object | 245 bytes | Public |
| `5FC107` | Card Capability Container | 287 bytes | Public |
| `5FC108` | Cardholder Facial Image | 512 bytes | PIN |
| `5FC109` | Printed Information | 245 bytes | PIN |
| `5FC10A` | Digital Signature Certificate | 3000 bytes | Public |
| `5FC10B` | Key Management Certificate | 3000 bytes | Public |
| `5FC10C` | Key History Object | 32 bytes | Public |
| `5FC10D`-`5FC120` | Retired Key Management Certificates | 3000 bytes each | Public |
| `5FC121` | Cardholder Iris Images | 512 bytes | PIN |
| `5FFF00` | Pairing Code Reference Data / Admin Data | 128 bytes | Public |
| `5FFF01` | Attestation Certificate | 3000 bytes | Public |

The `5FFF01` attestation certificate object is preserved by a PIV reset. Other writable data objects are cleared.

Certificate capacities are 3000 bytes on firmware version 1.6 and later and 1000 bytes on firmware version 1.5 and earlier.

## 5. Firmware 3.1.1 Extensions

### 5.1 AES-192 Management Key

The management key is 24 bytes and uses AES-192, algorithm ID `0A`. The default value is:

```text
01 02 03 04 05 06 07 08 01 02 03 04 05 06 07 08
01 02 03 04 05 06 07 08
```

Set Management Key uses the Yubico-compatible data form `0A 9B 18 <24-byte-key>`. `P2 = FF` disables touch for management-key authentication; `P2 = FE` requires touch.

### 5.2 Set PIN Retries

Set the PIN and PUK retry limits with:

```text
00 FA <pin-retries> <puk-retries>
```

The command has no data field. Both retry values must be between 1 and 15. Management-key authentication and PIN verification are both required before the command is sent.

A successful command resets the PIN to `123456` and the PUK to `12345678`, with the requested retry limits.

### 5.3 Move or Delete a Key

Move a key between slots with:

```text
00 F6 <destination-slot> <source-slot>
```

Delete a key by setting the destination slot to `FF`:

```text
00 F6 FF <source-slot>
```

Both commands have no data field and require management-key authentication. The destination slot must be empty. These operations affect only the private key and its metadata; certificate data objects are not moved or deleted.

### 5.4 Attest a Key

Request an attestation certificate with:

```text
00 F9 <slot> 00
```

The command has no data field and requires neither PIN verification nor management-key authentication. It returns a DER-encoded X.509 certificate signed by the P-256 key in slot `F9`.

Only keys generated on the device can be attested. Imported keys are rejected. The attestation key in slot `F9` and its issuer certificate in data object `5FFF01` must be provisioned before this command can be used; both survive a PIV reset.

## 6. Other Extension Commands

### 6.1 Get Metadata

Use `00 F7 00 <reference>` with no data. The reference can be PIN (`80`), PUK (`81`), management key (`9B`), or a supported asymmetric-key slot. The response follows the Yubico PIV metadata TLV format and reports values such as algorithm, policies, origin, public key, default status, and retry counters where applicable.

### 6.2 Get Serial and Version

- `00 F8 00 00` returns the four-byte device serial number.
- `00 FD 00 00` returns the three-byte PIV applet version.

### 6.3 Import Asymmetric Key

Use `INS = FE`, with the algorithm ID in `P1` and the destination slot in `P2`. Management-key authentication is required. The command supports ISO 7816-4 command chaining for key material that does not fit in one short APDU. The imported key format follows the Yubico PIV TLV format.

### 6.4 Reset

Use `00 FB 00 00` with no data. Reset is accepted only when both PIN and PUK are blocked. It restores PIV user data and defaults while preserving the attestation key in slot `F9` and the attestation certificate in data object `5FFF01`.
