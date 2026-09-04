+++
title = "PKCS#11 Integration"
date = 2026-09-04T00:00:00+08:00
weight = 10
+++

The [CanoKey PKCS#11 module](https://github.com/canokeys/canokey-pkcs11)
allows desktop applications to access the PIV applet through the standard
PKCS#11 interface. It provides the PKCS#11 3.2 interface and keeps the older
2.40 function list for applications that have not moved to 3.x.

## What it supports

The module discovers the PIV key slots `9A`, `9C`, `9D`, `9E`, and retired
slots `82` through `95`. Empty slots are omitted. PIV data objects that are
present on the card, including certificates, CHUID, and metadata, are exposed
as PKCS#11 objects as well.

| Capability | Support |
|:-----------|:--------|
| RSA 2048/3072/4096 | Generate/import, sign, and RSA decrypt |
| NIST P-256/P-384/P-521 | Generate/import, ECDSA sign/verify, and ECDH |
| Ed25519 | Generate/import and pure EdDSA signing |
| X25519 | Generate/import and ECDH derive |
| ML-DSA-65 | Generate/import, card signing, and host verification |
| ML-KEM-768 | Generate/import, host encapsulation, and card decapsulation |
| PIV data objects | Read when present; writes require management authorization |
| Random bytes from the card | Available on firmware 6.0 and later |

The PIV extension algorithm IDs are read from the card. Applications should
not hard-code the documented defaults when using a card whose algorithm
configuration has been changed.

## What it does not support

* SM2 keys can be discovered with their correct curve identity, but SM2
  signing and key agreement are not available through this module.
* ECDH and other one-step derivation operations with a PIN-always key are
  rejected because the current PIV protocol has no per-operation PIN step for
  them.
* Host-provided RNG seeding is not supported; the card seeds its own RNG.
* PKCS#11 message, asynchronous, and authenticated-wrap operation families are
  not supported.
* The module does not turn PIV keys into OpenPGP keys. OpenPGP applications
  must use the OpenPGP applet and its own integration path.

RSA public-key encryption and RSA, ECDSA, and ML-DSA verification are performed
run in the host library. Private-key signing, RSA decryption, ECDH, and ML-KEM
decapsulation run on the card.

## PIN and touch policies

CanoKey private keys support per-key PIN policies (never, once, or always) and
touch policies (never, always, or cached). The default is PIN-never for 9E and
PIN-once for the other supported PIV key slots; touch is never by default. A
PIN-never key can be used without logging in. PIN-once and PIN-always keys
require the user PIN. PIN-always signing and decryption require a fresh
per-operation authorization; a single login does not last forever.

## Working with common applications

### OpenSC

OpenSC can load the module directly. Use the build artifact for your platform,
for example `libcanokey-pkcs11.so` on Linux or `canokey-pkcs11.dll` on Windows.

```bash
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --list-slots
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --slot-index 0 --list-objects
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --slot-index 0 --login --list-objects
```

Use the object ID and mechanism reported by `--list-objects` and
`--mechanism-list` when signing or decrypting. A PKCS#11 slot index is not a
PIV slot number, so do not assume that slot index `2` means PIV slot `9D`.

### OpenSSL

OpenSSL does not load a PKCS#11 module by itself. Use a PKCS#11 provider (or a
compatible engine on older OpenSSL releases), then refer to the PIV key with a
PKCS#11 URI. A typical provider-based workflow looks like this:

```bash
openssl list -providers
openssl dgst -sha256 \
  -sign 'pkcs11:token=CanoKey;object=Digital%20Signature' \
  -out signature.bin message.bin
```

The provider configuration determines the exact URI attributes and command-line
options. Private-key operations are sent to the card; OpenSSL still handles
certificate parsing, hashing, and public-key verification.

### Adobe Acrobat

Acrobat can use a PKCS#11 security device for certificate-backed PDF
signatures. Add the CanoKey module in Acrobat's signature or security-device
settings, let Acrobat enumerate the certificate in a PIV slot, and select that
certificate when signing. The exact behavior depends on the Acrobat version
and the algorithms it supports. RSA and NIST EC certificates are the most
interoperable choices; Ed25519, X25519, SM2, and post-quantum PIV keys are not
expected to work with Acrobat's PDF signature formats.

### Other applications

Firefox and other software that accepts PKCS#11 modules can use the same
library for certificate selection and TLS client authentication, subject to the
application's algorithm support. GnuPG normally talks to a smart card through
`scdaemon` and the OpenPGP applet; it does not automatically use this PIV
PKCS#11 module.

## Standalone and managed use

Most applications use standalone mode, where the module discovers and manages
PC/SC readers itself. The Windows minidriver uses managed mode because Windows
already owns the card handle. Applications normally do not need to enable
managed mode; it is an implementation detail of the minidriver.

For installation and build instructions, see the
[canokey-pkcs11 repository](https://github.com/canokeys/canokey-pkcs11). For
Windows-specific behavior, see [Windows Minidriver](minidriver/).
