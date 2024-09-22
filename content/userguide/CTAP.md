+++
title = "WebAuthn"
date =  2021-01-16T01:30:15+08:00
weight = 15
+++

## Features

CanoKey's WebAuthn functionality adheres to the [CTAP 2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html), [CTAP 2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html), and [U2F](https://fidoalliance.org/specs/fido-u2f-v1.0-ps-20141009/fido-u2f-hid-protocol-ps-20141009.html) standards.

Supported features include:

- Up to 64 sets of discoverable credentials (resident keys)
- HMAC extension support
- Ed25519 algorithm
- Support for Discoverable Credentials management (firmware version ≧ 2.0.0)
- Support for PIN Protocol 2 (firmware version ≧ 2.0.0)
- Support for Credential Blob (firmware version ≧ 2.0.0)
- Support for Large Blob (firmware version ≧ 2.0.0)

## Primary Uses

### Multi-Factor Authentication

CanoKey can be used for two-factor authentication on many [websites](https://2fa.directory/int/).

{{% notice note %}}
By default, CanoKey does not set a PIN. Some websites and certain features (such as Discoverable Credentials management) require you to set a PIN. Please set it when prompted.
{{% /notice %}}

### SSH

#### 1. OpenSSH Version Requirement

To use FIDO keys for SSH authentication, ensure that the installed OpenSSH version supports this feature. The minimum version requirements are as follows:
- OpenSSH 8.2 and above

You can check the current `ssh` client and `sshd` service versions using the following commands:

```sh
ssh -V
sshd -V
```

#### 2. Using ECDSA-SK and ED25519-SK Key Pairs

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

#### 3. Using Discoverable Credential (Resident Key)

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

### PAM

Please refer to [pam-u2f](https://developers.yubico.com/pam-u2f/).

## HMAC-secret Extension

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html), used for LUKS full-disk encryption

{{% notice note %}}
Due to a [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) in the CTAP implementation, CanoKey firmware version ≤ 1.3 is incompatible with libfido2 1.7.0, and thus cannot be used with `systemd-cryptenroll`. Affected users should use libfido2 1.6.0.
{{% /notice %}}.
