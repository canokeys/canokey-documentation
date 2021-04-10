+++
title = "FIDO2/U2F"
date =  2021-01-16T01:30:15+08:00
weight = 15
+++

## Supports

The implementations are following [CTAP2](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) and [CTAP1/U2F](https://fidoalliance.org/specs/fido-u2f-v1.0-ps-20141009/fido-u2f-hid-protocol-ps-20141009.html) specifications.

Supported features:

- Up to 64 resident keys
- The HMAC extension
- Ed25519

## Multi-Factor Authentication

You can use your CanoKey as a 2FA device on many [websites](https://www.dongleauth.info/).

## PIN

The PIN is not set by default. You may set a new PIN using Windows Hello or other possible applications.

## OpenSSH

You may use the following command to generate a private key for ssh. See [here](https://undeadly.org/cgi?action=article;sid=20191115064850) for more info.

```
ssh-keygen -t ecdsa-sk
# or you prefer ed25519
ssh-keygen -t ed25519-sk
```

## PAM

Use `pam_u2f` provided by Yubico. One common scenario is `sudo`.

## HMAC-secret extension

Possible applications:

- [khefin](https://github.com/mjec/khefin), for LUKS full disk encryption.
- [systemd v248+](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html), for LUKS full disk encryption

  {{% notice note %}}
  Due to [a bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) in the CTAP implementation, Canokeys with firmware version <= 1.3 are incompatible with libfido2 1.7.0, and thus cannot be used with `systemd-cryptenroll`.

  Users with such key may try libfido2 1.6.0 instead.
  {{% /notice %}}

- [Windows Hello](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)
