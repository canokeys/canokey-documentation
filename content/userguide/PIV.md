+++
title = "PIV"
date =  2020-07-11T22:33:15+08:00
weight = 25
+++

PIV (Personal Identity Verification) is defined by the US federal government [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf) standard. PIV can store keys and certificates for signing and encryption, enabling functions such as digital signatures and file encryption.

## 1. Basic Information

### 1.1 Supported Algorithms

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

Firmware version 3.1.1 adds NIST P-521 (`secp521r1`, algorithm ID `15`). Management software can read the extended algorithm IDs currently used by the device; use the values returned by the device.

{{% notice note %}}
CanoKey firmware version 3.0.0 only supports signing 32-byte data using Ed25519 and only supports internally generated X25519 keys. Firmware version 3.0.2 and later are not affected by these limitations.
{{% /notice %}}

### 1.2 Default Values

* PIN: 123456
* PUK: 12345678
* Management Key: `010203040506070801020304050607080102030405060708`

On firmware version 3.1.1 and later, the 24-byte management key uses AES-192 (algorithm ID `0A`).

### 1.3 Key Slots

CanoKey supports the following key slots:

* 9A: PIV Authentication
* 9E: Card Authentication
* 9C: Digital Signature
* 9D: Key Management

Firmware version 2.0.0 adds Retired Key Management slots 82 and 83.

Firmware version 3.1.1 and later support all Retired Key Management slots from 82 through 95. These slots and their certificate objects are created only when used and consume available device storage.

### 1.4 PIN and Touch Policies

#### PIN Policy
* Never: Never verify PIN
* Always: Verify PIN for every use
* Once: Verify PIN once per session

#### Touch Policy
* Never: Never require touch
* Always: Require touch for every use
* Cached: No touch required if touched within the last 15 seconds, otherwise touch is required

#### Default Policies

{{% notice note %}}
Starting from firmware version 2.0.0, CanoKey supports configuring PIV PIN and touch policies.
{{% /notice %}}

| Key Slot | Default PIN Policy | Default Touch Policy |
|:---------|:-------------------|:---------------------|
| 9E       | Never              | Never                |
| 9C       | Always             | Never                |
| 9A, 9D, 82-95 | Once          | Never                |

The default shown for slot 9C applies to firmware version 3.1.1 and later. Defaults may differ on older firmware; read the PIV metadata to determine the policies in use on a device.

### 1.5 Data Size Limitations

* Certificate:
  * Firmware version 1.5 or earlier: 1000 bytes
  * Firmware version 1.6 or later: 3000 bytes
* Card Capability Container: 287 bytes
* Card Holder Unique Identifier: 2916 bytes
* Printed Information: 245 bytes
* Security Object: 245 bytes
* Cardholder Fingerprints, Facial Image, and Iris Images: 512 bytes each
* Key History: 32 bytes
* Admin Data: 128 bytes

Firmware version 3.1.1 and later support all Retired Key Management certificate objects. Reading Printed Information, fingerprints, facial images, or iris images requires PIN verification. Writing these objects requires management-key authentication. Optional objects consume storage only after data is written to them.

### 1.6 Other Features

Starting from firmware version 2.0.0, CanoKey supports viewing PIV metadata.

Firmware version 3.1.1 and later support:

* Setting the PIN and PUK retry counters from 1 to 15. This operation requires both management-key and PIN authentication, and resets PIN and PUK to their default values.
* PIV attestation for keys generated on the device (`INS F9`). Slot F9 must contain a provisioned P-256 attestation key, and its certificate must be stored in data object `5FFF01`. A PIV application reset preserves both items. Imported keys cannot be attested.
* Moving keys between PIV slots or deleting keys after management-key authentication (`INS F6`). The corresponding certificates are not moved or deleted with the keys.
* Reading and writing the PIN-protected PIV data objects described above.

## 2. Common Operations

{{% notice note %}}
As PIV is typically issued by system administrators and used by regular users, please review the documentation to understand the following content before proceeding.
{{% /notice %}}

### 2.1 Tools

It is recommended to use [yubico-piv-tool](https://developers.yubico.com/yubico-piv-tool/Releases/) for related operations.

### 2.2 Importing Keys and Certificates Separately

If the key and certificate are in two separate files, they need to be imported separately.

Importing the private key:
```sh
yubico-piv-tool -r canokey -a import-key -s 9a -i private-key.pem
```

Importing the certificate:
```sh
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

Here, `-s 9a` indicates using the 9A key slot, which can be changed as needed.

### 2.3 Importing PKCS#12 File

To import a PKCS#12 file (.p12 or .pfx) containing both the private key and certificate, execute:
```sh
yubico-piv-tool -r canokey -a import-key -a import-certificate -K PKCS12 -s 9a -i certificate.p12
```

### 2.4 Generating Key and Self-signing

Generate a new private key and self-sign it:
```sh
yubico-piv-tool -r canokey -a generate -s 9a -A RSA2048 -o public-key.pem
yubico-piv-tool -r canokey -a verify-pin -a selfsign -s 9a -S "/CN=Test Certificate" -i public-key.pem -o certificate.pem
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

### 2.5 Additional Steps for Windows

Since Windows caches certificate information based on CHUID, you need to update the CHUID after certificate import on Windows:
```sh
yubico-piv-tool -r canokey -a set-chuid
```
