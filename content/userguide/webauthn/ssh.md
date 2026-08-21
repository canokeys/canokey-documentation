+++
title = "SSH"
date = 2021-01-16T01:30:15+08:00
weight = 4
+++

CanoKey can be used for SSH authentication with FIDO keys.

## OpenSSH Version Requirement

To use FIDO keys for SSH authentication, ensure that the installed OpenSSH version supports this feature. The minimum version requirements are as follows:
- OpenSSH 8.2 and above

You can check the current `ssh` client and `sshd` service versions using the following commands:

```sh
ssh -V
sshd -V
```

## Using ECDSA-SK and ED25519-SK Key Pairs

### Creating ECDSA-SK and ED25519-SK Key Pairs

1. Create an ECDSA-SK key pair:

```sh
ssh-keygen -t ecdsa-sk -f ~/.ssh/id_ecdsa_sk
```

2. Create an ED25519-SK key pair:

```sh
ssh-keygen -t ed25519-sk -f ~/.ssh/id_ed25519_sk
```

### Adding the Public Key to the Target Server

Add the content of the generated public key file (`~/.ssh/id_ecdsa_sk.pub` or `~/.ssh/id_ed25519_sk.pub`) to the `~/.ssh/authorized_keys` file on the target server.

You can use the following command to copy the public key to the remote server:

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

Or

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

### Using on Other Machines

Copy the generated private key file (`~/.ssh/id_ecdsa_sk` or `~/.ssh/id_ed25519_sk`) to other machines where it needs to be used. Ensure the correct file permissions:

```sh
chmod 600 ~/.ssh/id_ecdsa_sk
chmod 600 ~/.ssh/id_ed25519_sk
```

## Using Discoverable Credential (Resident Key)

{{% notice note %}}
CanoKey firmware version must be at least 2.0.0.
{{% /notice %}}

### Creating RK Keys

1. Create an ECDSA-SK RK:

```sh
ssh-keygen -t ecdsa-sk -O resident -f ~/.ssh/id_ecdsa_sk
```

2. Create an ED25519-SK RK:

```sh
ssh-keygen -t ed25519-sk -O resident -f ~/.ssh/id_ed25519_sk
```

### Adding the Public Key to the Target Server

Similar to the non-RK keys above, add the content of the generated public key file to the server's `~/.ssh/authorized_keys` file.

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

Or

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```
