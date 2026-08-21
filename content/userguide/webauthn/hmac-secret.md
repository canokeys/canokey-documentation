+++
title = "HMAC-secret Extension"
date = 2021-01-16T01:30:15+08:00
weight = 6
+++

CanoKey supports the HMAC-secret extension, which allows applications to derive a secret from the key.

## systemd-cryptenroll

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html), used for LUKS full-disk encryption

{{% notice note %}}
Due to a [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) in the CTAP implementation, CanoKey firmware version ≤ 1.3 is incompatible with libfido2 1.7.0, and thus cannot be used with `systemd-cryptenroll`. Affected users should use libfido2 1.6.0.
{{% /notice %}}
