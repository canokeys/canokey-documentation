+++
title = "WebAuthn"
date =  2022-06-08T20:30:05+08:00
weight = 15
+++

## 1. 特性

CanoKey 的 WebAuthn 功能遵循 [CTAP 2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html)、
[CTAP 2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) 以及 [U2F](https://fidoalliance.org/specs/fido-u2f-v1.0-ps-20141009/fido-u2f-hid-protocol-ps-20141009.html) 标准。

支持的特性有：

- 高达 64 组 discoverable credentials (resident keys)
- 支持 HMAC 扩展
- Ed25519 算法

从固件版本 2.0.0 起，还支持如下特性：
- Discoverable Credentials 管理
- PIN Protocol 2
- Credential Blob
- Large Blob

从固件版本 3.0.0 起，实验性支持 SM2 算法。

## 2. 常见用途

### 2.1 多因素认证

CanoKey 可以用于很多[网站](https://2fa.directory/int/)的双因素认证。

{{% notice note %}}
CanoKey 默认不设置 PIN，部分网站及部分功能（如 Discoverable Credentials 管理）要求您必须设置 PIN，请在收到提示时设置。
{{% /notice %}}

### 2.2 SSH

#### 2.2.1 OpenSSH 版本要求

为了使用 FIDO 密钥进行 SSH 身份验证，需要确保安装的 OpenSSH 版本支持该功能。最低版本要求如下：
- OpenSSH 8.2 及以上版本

您可以使用以下命令检查当前的 `ssh` 客户端和 `sshd` 服务版本：

```sh
ssh -V
sshd -V
```

#### 2.2.2 使用 ECDSA-SK 和 ED25519-SK 密钥对

##### 创建 ECDSA-SK 和 ED25519-SK 密钥对

1. 创建一个 ECDSA-SK 密钥对：

```sh
ssh-keygen -t ecdsa-sk -f ~/.ssh/id_ecdsa_sk
```

2. 创建一个 ED25519-SK 密钥对：

```sh
ssh-keygen -t ed25519-sk -f ~/.ssh/id_ed25519_sk
```

##### 将公钥添加到目标服务器

将生成的公钥文件（`~/.ssh/id_ecdsa_sk.pub` 或者 `~/.ssh/id_ed25519_sk.pub`）的内容添加到目标服务器的 `~/.ssh/authorized_keys` 文件中。

您可以使用以下命令将公钥复制到远程服务器：

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

或者

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

##### 在其他机器上使用

将生成的私钥文件（`~/.ssh/id_ecdsa_sk` 或者 `~/.ssh/id_ed25519_sk`）复制到需要使用的其他机器上。确保文件权限正确：

```sh
chmod 600 ~/.ssh/id_ecdsa_sk
chmod 600 ~/.ssh/id_ed25519_sk
```

#### 2.2.3 使用 Discoverable Credential（Resident Key）

{{% notice note %}}
CanoKey 固件版本最低要求为 2.0.0。
{{% /notice %}}

##### 创建 RK 密钥

1. 创建一个 ECDSA-SK RK：

```sh
ssh-keygen -t ecdsa-sk -O resident -f ~/.ssh/id_ecdsa_sk
```

2. 创建一个 ED25519-SK RK：

```sh
ssh-keygen -t ed25519-sk -O resident -f ~/.ssh/id_ed25519_sk
```

##### 将公钥添加到目标服务器

与上述非 RK 密钥相同，将生成的公钥文件内容添加到服务器的 `~/.ssh/authorized_keys` 文件中。

```
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

或者

```
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

### 2.3 PAM

请参阅 [pam-u2f](https://developers.yubico.com/pam-u2f/)。

### 2.4 HMAC-secret 扩展

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html)，用于 LUKS 全盘加密

{{% notice note %}}
受 CTAP 实现中的一处 [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) 影响，固件版本小于等于 `1.3` 的 CanoKey 与 libfido2 1.7.0 不兼容，因此不能用于 `systemd-cryptenroll`。
受影响的用户请使用 libfido2 1.6.0。
{{% /notice %}}
