+++
title = "WebAuthn (Passkey)"
date =  2021-01-16T01:30:15+08:00
weight = 15
+++

## 1. Features

CanoKey's WebAuthn functionality supports CTAP 2.0. CTAP 2.1 is supported from firmware version 2.0.0, and CTAP 2.3 is supported from firmware version 3.1.1.

Supported features include:

- Discoverable Credentials (Resident Keys)
- HMAC extensions
- Ed25519 algorithm

Firmware version 2.0.0 adds:

- Discoverable Credentials management
- PIN Protocol 2
- `credProtect`, `credBlob`, and `largeBlobKey` extensions
- Large Blob

Firmware version 3.0.0 adds:

- SM2 algorithm

Firmware version 3.1.1 adds:

- Configuration options including `alwaysUv`, minimum PIN length, forced PIN change, and long-press reset settings
- `minPinLength`, `thirdPartyPayment`, and HMAC-secret during credential creation (`hmac-secret-mc`) extensions
- ML-DSA-65 (`alg = -49`) credentials; the default algorithm ID for SM2 changes from `-48` to `-54`
- No fixed limit of 64 Discoverable Credentials: actual capacity depends on available device storage, and the device also reports the remaining capacity
- U2F support when `alwaysUv` is disabled

{{% notice note %}}
Firmware version 3.0.0 does not support U2F, WebAuthn over USB on iOS 17.4 and 18, or WebAuthn on macOS, including Safari, Firefox, and applications that rely on Apple's CTAP stack. Firmware version 3.0.2 and later are not affected by these limitations.
{{% /notice %}}

## 2. Primary Uses

### 2.1 Multi-Factor Authentication

CanoKey can be used for two-factor authentication on many [websites](https://2fa.directory/int/).

{{% notice note %}}
By default, CanoKey does not set a PIN. Some websites and certain features (such as Discoverable Credentials management) require you to set a PIN. Please set it when prompted.
{{% /notice %}}

### 2.2 SSH

#### 2.2.1 OpenSSH Version Requirement

To use FIDO keys for SSH authentication, ensure that the installed OpenSSH version supports this feature. The minimum version requirements are as follows:
- OpenSSH 8.2 and above

You can check the current `ssh` client and `sshd` service versions using the following commands:

```sh
ssh -V
sshd -V
```

#### 2.2.2 Using ECDSA-SK and ED25519-SK Key Pairs

##### Creating ECDSA-SK and ED25519-SK Key Pairs

1. Create an ECDSA-SK key pair:

```sh
ssh-keygen -t ecdsa-sk -f ~/.ssh/id_ecdsa_sk
```

2. Create an ED25519-SK key pair:

```sh
ssh-keygen -t ed25519-sk -f ~/.ssh/id_ed25519_sk
```

##### Adding the Public Key to the Target Server

Add the content of the generated public key file (`~/.ssh/id_ecdsa_sk.pub` or `~/.ssh/id_ed25519_sk.pub`) to the `~/.ssh/authorized_keys` file on the target server.

You can use the following command to copy the public key to the remote server:

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

Or

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

##### Using on Other Machines

Copy the generated private key file (`~/.ssh/id_ecdsa_sk` or `~/.ssh/id_ed25519_sk`) to other machines where it needs to be used. Ensure the correct file permissions:

```sh
chmod 600 ~/.ssh/id_ecdsa_sk
chmod 600 ~/.ssh/id_ed25519_sk
```

#### 2.2.3 Using Discoverable Credential (Resident Key)

{{% notice note %}}
CanoKey firmware version must be at least 2.0.0.
{{% /notice %}}

##### Creating RK Keys

1. Create an ECDSA-SK RK:

```sh
ssh-keygen -t ecdsa-sk -O resident -f ~/.ssh/id_ecdsa_sk
```

2. Create an ED25519-SK RK:

```sh
ssh-keygen -t ed25519-sk -O resident -f ~/.ssh/id_ed25519_sk
```

##### Adding the Public Key to the Target Server

Similar to the non-RK keys above, add the content of the generated public key file to the server's `~/.ssh/authorized_keys` file.

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

Or

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

### 2.3 PAM

Please refer to [pam-u2f](https://developers.yubico.com/pam-u2f/).

{{% notice note %}}
Firmware version 3.0.0 does not support U2F. Firmware version 3.0.2 and later support U2F. On firmware version 3.1.1 and later, `alwaysUv` must be disabled.
{{% /notice %}}

### 2.4 HMAC-secret Extension

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html), used for LUKS full-disk encryption

{{% notice note %}}
Due to a [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) in the CTAP implementation, CanoKey firmware version ≤ 1.3 is incompatible with libfido2 1.7.0, and thus cannot be used with `systemd-cryptenroll`. Affected users should use libfido2 1.6.0.
{{% /notice %}}
