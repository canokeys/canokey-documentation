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

Starting from CanoKey Canary, the following extended algorithms are also supported:

| Algorithm Name | Algorithm ID |
|:---------------|:-------------|
| RSA3072        | 05           |
| RSA4096        | 16           |
| secp256k1      | 53           |
| Ed25519        | E0           |
| X25519         | E1           |
| SM2            | 54           |

### 1.2 Default Values

* PIN: 123456
* PUK: 12345678
* Management Key: `010203040506070801020304050607080102030405060708`

### 1.3 Key Slots

CanoKey supports the following key slots:

* 9A: PIV Authentication
* 9E: Card Authentication
* 9C: Digital Signature
* 9D: Key Management

Starting from firmware version 2.0.0, CanoKey also supports the following key slots:

* 82, 83

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
| Others   | Once               | Never                |

### 1.5 Data Size Limitations

* Certificate:
  * Firmware version 1.5 or earlier: 1000 bytes
  * Firmware version 1.6 or later: 3000 bytes
* Card Capability Container: 287 bytes
* Card Holder Unique Identifier: 2916 bytes
* Printed Information: 245 bytes

### 1.6 Other Features

Starting from firmware version 2.0.0, CanoKey supports viewing PIV metadata.

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

Since Windows caches certificate information based on CHUID, you need to update the CHUID after each certificate import on Windows:
```sh
yubico-piv-tool -r canokey -a set-chuid
```