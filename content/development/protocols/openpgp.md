---
title: "OpenPGP Applet"
date: 2026-08-21T00:00:00+08:00
weight: 20
---

The CanoKey OpenPGP applet implements all mandatory features of the [OpenPGP smart card application specification, version 3.4](https://gnupg.org/ftp/specs/OpenPGP-smart-card-application-3.4.pdf), plus a set of optional and vendor-specific features. This page documents the commands exposed by the implementation and CanoKey-specific behavior. Refer to the specification for the standard command and data object definitions.

## 1. Application and Transport

### 1.1 AID

The OpenPGP application identifier prefix is:

```text
D2 76 00 01 24 01
```

Select it with `00 A4 04 00 06 D27600012401`. The full AID, returned in the Application Related Data, appends the version bytes `03 04`, the manufacturer ID `F1 D0`, the four-byte device serial number, and two trailing zero bytes.

### 1.2 APDU Transport

CanoKey accepts short APDUs. Commands use `CLA = 00`; firmware version 3.1.1 and later also accept ISO 7816-4 command chaining (`CLA = 10`) for:

- Put Data of the cardholder certificate (`INS = DA`, `P1 = 7F`, `P2 = 21`)
- Import Key (`INS = DB`)
- PSO Decipher (`INS = 2A`, `P1 = 80`, `P2 = 86`)

When response data does not fit in one response, CanoKey returns `61xx`. Use Get Response (`INS = C0`) to retrieve the remaining data.

### 1.3 Instructions

| INS | Name | Definition |
|:---:|:-----|:-----------|
| `20` | Verify | OpenPGP card specification |
| `24` | Change Reference Data | OpenPGP card specification |
| `2A` | PSO: Compute Digital Signature / Decipher | OpenPGP card specification |
| `2C` | Reset Retry Counter | OpenPGP card specification |
| `44` | Activate File | OpenPGP card specification |
| `47` | Generate Asymmetric Key Pair | OpenPGP card specification |
| `84` | Get Challenge | OpenPGP card specification |
| `88` | Internal Authenticate | OpenPGP card specification |
| `A4` | Select | ISO 7816-4 |
| `A5` | Select Data | OpenPGP card specification |
| `C0` | Get Response | ISO 7816-4 |
| `CA` | Get Data | OpenPGP card specification |
| `CC` | Get Next Data | OpenPGP card specification |
| `DA` | Put Data | OpenPGP card specification |
| `DB` | Import Key | OpenPGP card specification |
| `E6` | Terminate DF | OpenPGP card specification |
| `F2` | Set PIN Retries | CanoKey extension, 3.1.1+ |

The following optional features of the specification are not supported: KDF, Secure Messaging, AES, and the Manage Security Environment command.

## 2. PINs and Retry Counters

The applet uses three passwords: the user PIN (PW1, references `81` and `82`), the Admin PIN (PW3, reference `83`), and the optional Reset Code (RC).

| Item | Default | Minimum length | Maximum length | Default retries |
|:-----|:-------:|:--------------:|:--------------:|:---------------:|
| PIN (PW1) | `123456` | 6 | 64 | 3 |
| Admin PIN (PW3) | `12345678` | 8 | 64 | 3 |
| Reset Code (RC) | Not set | 8 | 64 | 3 |

Verify uses `P1 = 00` with `P2 = 81` (PW1 for signatures), `P2 = 82` (PW1 for decryption and authentication), or `P2 = 83` (PW3). `P1 = FF` resets the corresponding verification state. An empty data field queries the status without verifying: the applet returns `9000` if already verified, `63Cx` with the remaining retries, or `6983` if blocked.

The first byte of the PW Status data object (`C4`) controls whether PW1 verified with reference `81` is consumed by each signature (`00`, the factory default: verification is required for every PSO signature) or kept (`01`).

Reset Retry Counter accepts `P1 = 00` with the Reset Code in the data field (rejected with `6982` when no Reset Code is set), or `P1 = 02` with Admin PIN verification. In both cases a new PIN follows in the data field.

### 2.1 Set PIN Retries (3.1.1+)

Firmware version 3.1.1 and later allow configuring the retry limits with:

```text
00 F2 00 00 03 <pw1-retries> <rc-retries> <pw3-retries>
```

Each value must be between 1 and 15. Admin PIN (PW3) verification is required. A successful command resets the PIN to `123456` and the Admin PIN to `12345678` with the requested retry limits; an existing Reset Code value is preserved, but its retry counter is updated.

## 3. Keys and Algorithms

### 3.1 Key Slots

Three key slots are available, identified by the control reference template in the data field of Generate Asymmetric Key Pair and Import Key:

| CRT tag | Slot | Key reference |
|:-------:|:-----|:-------------:|
| `B6` | Signature (SIG) | `01` |
| `B8` | Decryption (DEC) | `02` |
| `A4` | Authentication (AUT) | `03` |

### 3.2 Algorithm Attributes

The algorithm attributes data objects `C1` (SIG), `C2` (DEC), and `C3` (AUT) are writable with Admin PIN verification. Changing the attributes of a slot deletes the key stored in it. The DEC slot does not accept Ed25519; the SIG and AUT slots do not accept X25519.

| Algorithm | Attributes format | Availability |
|:----------|:------------------|:-------------|
| RSA 2048 / 3072 / 4096 | `01` + modulus bits (2 bytes) + exponent bits (2 bytes, `0020`) | RSA 2048: all firmware versions; RSA 3072 / 4096 generation: 2.0.0+ |
| ECDSA / ECDH NIST P-256 | `13` / `12` + OID `2A 86 48 CE 3D 03 01 07` | All supported firmware versions |
| ECDSA / ECDH secp256k1 | `13` / `12` + OID `2B 81 04 00 0A` | All supported firmware versions |
| ECDSA / ECDH NIST P-384 | `13` / `12` + OID `2B 81 04 00 22` | All supported firmware versions |
| ECDSA / ECDH NIST P-521 | `13` / `12` + OID `2B 81 04 00 23` | 3.1.1+ |
| Ed25519 (SIG, AUT) | `16` + OID `2B 06 01 04 01 DA 47 0F 01` | All supported firmware versions |
| X25519 (DEC) | `12` + OID `2B 06 01 04 01 97 55 01 05 01` | All supported firmware versions |
| SM2 | `13` / `12` + OID `06 08 2A 81 1C CF 55 01 82 2D` | 2.0.0+ |

Firmware versions 1.6.1 and earlier only support RSA public keys with e = 65537; on-card RSA generation in these versions is limited to RSA 2048 (RSA 4096 can be imported only). Firmware version 2.0.0 and later support RSA 3072 / 4096 key generation. Generated RSA keys always use e = 65537.

### 3.3 Generate Asymmetric Key Pair

```text
00 47 <P1> 00 <Lc> <CRT>
```

`P1 = 80` generates a new key in the slot selected by the CRT tag in the data field (`B6 00`, `B8 00`, or `A4 00`; the five-byte form such as `B6 03 84 01 01` is also accepted). `P1 = 81` reads the public key of an existing key and fails with `6A88` if the slot is empty. The response is a `7F49` public key data object. Generating a new SIG key resets the digital signature counter.

Unlike Put Data and Import Key, this command does not require Admin PIN verification.

### 3.4 PSO and Internal Authenticate

- PSO Compute Digital Signature: `00 2A 9E 9A`. Requires PW1 verified with reference `81`. For RSA keys the DigestInfo input must not exceed 40% of the modulus length; for ECDSA keys the digest is left-padded with zeros to the private key length. Each successful signature increments the digital signature counter (data object `93` inside the Security Support Template `7A`).
- PSO Decipher: `00 2A 80 86`. Requires PW1 verified with reference `82`. For RSA keys the data field starts with the padding indicator byte `00` followed by the ciphertext; RSA PKCS#1 v1.5 padding is removed from the result. For ECDH keys the data field is the Cipher DO `A6` containing a Public Key DO `7F49` with the external public key in tag `86` (`04 || x || y` for short Weierstrass curves, `x` for X25519); the shared secret is returned.
- Internal Authenticate: `00 88 00 00`. Uses the AUT key and requires PW1 verified with reference `82`. Input handling is identical to PSO Compute Digital Signature.

### 3.5 Import Key

```text
00 DB 3F FF <Lc> <extended header list>
```

Requires Admin PIN verification. The data field is the extended header list `4D` containing the control reference template (`B6`, `B8`, or `A4`) followed by the private key template (`7F48` and `5F48`) as defined by the specification. The algorithm attributes of the target slot must be set before importing. Importing a SIG key resets the digital signature counter.

### 3.6 Get Challenge

`00 84 00 00 <Le>` returns `Le` random bytes.

## 4. Data Objects

### 4.1 Get Data

Get Data uses `INS = CA` with the tag in `P1:P2` and an empty data field. Directly readable tags:

| Tag | Data object | Notes |
|:---:|:------------|:------|
| `4F` | AID | Full AID including the device serial number |
| `5E` | Login data | Maximum 63 bytes |
| `5F50` | URL | Maximum 255 bytes |
| `5F52` | Historical bytes | Constant |
| `65` | Cardholder Related Data | Contains name `5B` (39 bytes), language `5F2D` (8 bytes), sex `5F35` (1 byte) |
| `6E` | Application Related Data | Constructed; see below |
| `7A` | Security Support Template | Contains the digital signature counter `93` |
| `7F21` | Cardholder certificate | Maximum 1152 bytes; see Section 4.3 |
| `7F66` | Extended Length Info | Constant |
| `7F74` | General Feature Management | Announces a button for user confirmation |
| `C4` | PW Status | PIN strategy byte, lengths, and retry counters |
| `DE` | Key Info | Key references with origin (not present / generated / imported) |
| `FA` | Algorithm Information | Supported algorithm attributes per slot |
| `0102` | Touch cache time | CanoKey extension; one byte, in seconds |

The Application Related Data `6E` contains the AID `4F`, Historical Bytes `5F52`, Extended Length Info `7F66`, General Feature Management `7F74`, and the Discretionary Data Objects `73` (Extended Capabilities `C0`, Algorithm Attributes `C1`-`C3`, PW Status `C4`, Fingerprints `C5`, CA Fingerprints `C6`, Key Generation Dates `CD`, Key Info `DE`, and the UIF objects `D6`-`D8`).

### 4.2 Put Data

Put Data uses `INS = DA` with the tag in `P1:P2`. All writes require Admin PIN verification. Writable tags: `5B`, `5E`, `5F2D`, `5F35`, `5F50`, `7F21` (with command chaining on firmware 3.1.1 and later), `C1`-`C3` (algorithm attributes), `C4` (first byte only, `00` or `01`), `C7`-`C9` (key fingerprints), `CA`-`CC` (CA fingerprints), `CE`-`D0` (key generation dates), `D3` (Reset Code; an empty data field removes it), `D6`-`D8` (UIF), and `0102` (touch cache time).

### 4.3 Certificate Occurrences

Select Data (`INS = A5`, `P1 = 00`-`02`, `P2 = 04`, data `60 04 5C 02 7F 21`) selects the SIG, DEC, or AUT certificate occurrence for the following Get Data or Put Data on `7F21`. After a Get Data on `7F21`, Get Next Data (`INS = CC`, `P1:P2 = 7F21`) returns the next occurrence.

## 5. Touch Policy

Each key slot has a User Interaction Flag (UIF) data object — `D6` (SIG), `D7` (DEC), `D8` (AUT) — holding two bytes: the policy and the confirmation type (`20`, button). The policy values are:

| Value | Meaning |
|:-----:|:--------|
| `00` | Off: no touch required |
| `01` | On: touch required, cached for the touch cache time |
| `02` | Permanently on: touch required, cannot be changed back |

Setting the policy requires Admin PIN verification. A policy of `02` cannot be modified afterwards (rejected with `6985`). The touch cache time is stored in the one-byte data object `0102` (0-255 seconds; `00`, the factory default, disables the cache).

Touch is only enforced over the USB interface; over NFC no touch is requested. If the touch times out or is cancelled, the command fails with `6600`.

The same settings can be managed through the Admin applet instruction `09h` (SIG / DEC / AUT touch policy and cache time); see the [Admin Applet documentation](../admin/).

## 6. Terminate and Activate

Terminate DF (`00 E6 00 00`) requires Admin PIN verification while PW3 is not blocked; once PW3 is blocked it can be executed without verification. After termination, all commands except Select and Activate File fail with `6285`.

Activate File (`00 44 00 00`) is accepted only in the terminated state and restores the applet to its factory state: all keys, certificates, data objects, and PIN configuration are reset to the defaults described above.

## 7. Status Codes

| SW | Meaning |
|:---|:--------|
| `9000` | Success |
| `6285` | Applet is terminated |
| `63Cx` | Verification failed, x retries left |
| `6600` | Touch timed out or was cancelled |
| `6700` | Wrong length |
| `6982` | Security status not satisfied (verification required) |
| `6983` | Password blocked |
| `6985` | Conditions not satisfied |
| `6A80` | Wrong data |
| `6A82` | File or application not found |
| `6A86` | Wrong P1 / P2 |
| `6A88` | Referenced data not found |
| `6D00` | Instruction not supported |
| `6E00` | Class not supported |
