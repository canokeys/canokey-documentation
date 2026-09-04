+++
title = "Windows Minidriver"
date = 2026-09-04T00:00:00+08:00
weight = 11
+++

The [CanoKey Windows minidriver](https://github.com/canokeys/canokey-mini-driver)
allows Windows applications to use PIV certificates and private keys through the
Microsoft Smart Card Cryptographic Service Provider (CSP) and Smart Card Key
Storage Provider (KSP). It is a Windows integration layer; the complete PIV
inventory and algorithm set remains available through [PKCS#11](pkcs11/).

## What Windows exposes

The minidriver presents six stable Windows containers:

| Container index | PIV slot | Windows use |
|:---------------:|:--------:|:------------|
| 0 | `9A` | authentication and signature |
| 1 | `9C` | signature |
| 2 | `9D` | signature; RSA key exchange/decryption |
| 3 | `9E` | authentication and signature |
| 4 | `82` | signature and retired key management |
| 5 | `83` | signature and retired key management |

Windows supports RSA and NIST P-256, P-384, and P-521 in these containers.
Ed25519, X25519, secp256k1, SM2, ML-DSA, ML-KEM, and later retired slots are
available only through PKCS#11 because the current Windows smart-card
interface has no safe representation for them. An EC key is identified from its complete curve
parameters; a 32-byte coordinate is not enough to make it a P-256 key.

## Development installation

The development flow does not install the INF. Build the DLL, copy it to the
debug directory, and map the CanoKey ATR to it in the Calais registry key.
The default debug path is `C:\canokey-minidriver\canokey-minidriver.dll`.
The minidriver DLL is registered under the `80000001` value:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\Calais\SmartCards\CanoKey]
"ATR"=hex:3b,f7,11,00,00,81,31,fe,65,43,61,6e,6f,6b,65,79,99
"ATRMask"=hex:ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff
"Crypto Provider"="Microsoft Base Smart Card Crypto Provider"
"Smart Card Key Storage Provider"="Microsoft Smart Card Key Storage Provider"
"80000001"="C:\\canokey-minidriver\\canokey-minidriver.dll"
```

This registry-only mapping is for local debugging. It does not install or
register a production driver package. After replacing the DLL, unplug and
reinsert the token or restart any application that loaded the old module.

## Runtime configuration

Optional behavior is read from `HKLM\SOFTWARE\Canokeys\ckmd`:

| Value | Type and values | Default / purpose |
|:------|:----------------|:-----------------|
| `LogPath` | string | absent disables logging; a file path enables it |
| `LogLevel` | `trace`, `debug`, `info`, `warn`, `error`, `fatal`, `none` | `warn` when logging is enabled |
| `LogSensitiveData` | DWORD or boolean text | `0`; raw APDU/hex dumps when enabled |
| `ProtectManagement` | DWORD or boolean text | `1`; try PIN-managed management-key recovery after user PIN login |
| `RefreshDeviceKeys` | DWORD or boolean text | `1`; refresh external key/certificate changes |
| `RefreshWindow` | seconds | `60`; `0` refreshes every live metadata read |
| `NewKeyTouchPolicy` | `1` never, `2` always, `3` cached | `1` for keys created by Windows |
| `NewKeyPinPolicy` | `1` never, `2` once, `3` always | absent uses PIV defaults; `3` is currently rejected |
| `PinCacheTimeout` | seconds | value reported as the Windows PIN-cache recommendation |

Logging is best-effort. Keep `LogSensitiveData` disabled unless a local APDU
debug trace is specifically required; such logs may contain authentication or
private-operation data.

## PIN-managed operation

The PIN-protected management-key mode and its permanent PUK tradeoff are
described in [PIN, PUK, and Management Key](pin-puk-management-key/). The
minidriver uses this mode automatically after user authentication when the card
has been provisioned for it.

Leave `ProtectManagement=1` (the default) to enable that automatic check. Set
it to `0` when an external provisioning system owns management-key
authentication and the extra check is not wanted. This setting does not
provision the card or block the PUK. If the protected data is missing,
malformed, or the PUK is not blocked, the minidriver keeps only normal user
authentication and management operations need another supported admin path.

## Certificates, cache, and limitations

Certificate files are exposed for Windows enumeration and signing. Certificate
writes and key generation/import still require management authorization. The
minidriver keeps `cardid`, `cardcf`, and `mscp/cmapfile` available for Windows
cache coordination, but live PIV metadata remains authoritative. PIV has no
durable PIN freshness counter, so the reported cache mode is no-cache.

Use `certutil -scinfo` to confirm that Windows can enumerate the reader and
certificates. The minidriver repository also provides PowerShell tests for
CAPI/CNG signing, RSA decryption, and ECDH raw-secret derivation. A capability
that is not visible in KSP is not necessarily absent from the card; check the
[PKCS#11 interface](pkcs11/) for the full inventory.
