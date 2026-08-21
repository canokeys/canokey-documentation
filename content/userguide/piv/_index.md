+++
title = "PIV"
date = 2020-07-11T22:33:15+08:00
weight = 25
+++

PIV (Personal Identity Verification) is defined by the US federal government [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf) standard. PIV can store keys and certificates for signing and encryption, enabling functions such as digital signatures and file encryption. The CanoKey PIV applet implements the mandatory features of [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf), plus a set of Yubico-compatible and CanoKey-specific extensions; protocol-level details are documented in the [development doc](/development/protocols/piv/).

## Supported Algorithms

* RSA2048
* NIST P-256
* NIST P-384

Firmware version 3.0.0 and later also support the following extended algorithms:

| Algorithm Name | Algorithm ID |
|:---------------|:-------------|
| RSA3072        | 05           |
| RSA4096        | 16           |
| secp256k1      | 53           |
| Ed25519        | E0           |
| X25519         | E1           |
| SM2            | 54           |

Firmware version 3.1.1 and later also support:

| Algorithm Name | Algorithm ID |
|:---------------|:-------------|
| NIST P-521 (`secp521r1`) | 15 |
| ML-DSA-65 | E2 |
| ML-KEM-768 | E3 |

The IDs of the extended algorithms are configurable; management software can read the extended algorithm IDs currently used by the device and should use the values returned by the device.

{{% notice note %}}
CanoKey firmware version 3.0.0 only supports signing 32-byte data using Ed25519 and only supports internally generated X25519 keys. Firmware version 3.0.2 and later are not affected by these limitations.
{{% /notice %}}

## In This Chapter

* [Key Slots](slots/) — the PIV slots and what each one holds
* [PIN, PUK, and Management Key](pin-puk-management-key/) — the three secrets that protect the applet
* [PIN and Touch Policies](pin-touch-policies/) — per-slot verification and touch requirements
* [Certificates](certificates/) — certificate objects and their capacities
* [Data Objects](data-objects/) — the other PIV data objects and their size limits
* [Attestation](attestation/) — proving that a key was generated on the device
* [Cryptographic Operations](crypto-operations/) — signing, decryption, and key agreement
* [Metadata and Key Management](metadata/) — reading metadata, moving and deleting keys
* [Common Operations](operations/) — everyday tasks with yubico-piv-tool
