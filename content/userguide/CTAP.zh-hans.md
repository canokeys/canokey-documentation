+++
title = "WebAuthn (Passkey)"
date =  2022-06-08T20:30:05+08:00
weight = 15
+++

## 1. 特性

CanoKey 的 WebAuthn 功能支持 CTAP 2.0。从固件版本 2.0.0 起支持 CTAP 2.1，从固件版本 3.1.1 起支持 CTAP 2.3。

支持的特性有：

- Discoverable Credentials（Resident Keys）
- HMAC 扩展
- Ed25519 算法

固件版本 2.0.0 起，新增：
- Discoverable Credentials 管理
- PIN Protocol 2
- `credProtect`、`credBlob` 和 `largeBlobKey` 扩展
- Large Blob

固件版本 3.0.0 起，新增：
- SM2 算法

固件版本 3.1.1 起，新增：

- 配置功能，包括 `alwaysUv`、最小 PIN 长度、强制更改 PIN 和长按重置设置
- `minPinLength`、`thirdPartyPayment` 和创建凭据时的 HMAC-secret（`hmac-secret-mc`）扩展
- ML-DSA-65（`alg = -49`）凭据；SM2 的默认算法 ID 由 `-48` 改为 `-54`
- Discoverable Credential 的数量不再固定为 64 个，实际容量取决于设备的可用存储空间；设备还会报告当前剩余容量
- 关闭 `alwaysUv` 后可使用 U2F

{{% notice note %}}
固件版本 3.0.0：不支持 U2F，不支持通过 USB 在 iOS 17.4 和 18 上使用 WebAuthn，也不支持在 macOS（包括 Safari、Firefox，以及依赖 Apple CTAP 栈的应用程序）上使用 WebAuthn。固件 3.0.2 及更高版本不受这些限制。
{{% /notice %}}

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

{{% notice note %}}
固件版本 3.0.0 不支持 U2F；固件 3.0.2 及更高版本支持 U2F。使用固件 3.1.1 及更高版本时，需要关闭 `alwaysUv`。
{{% /notice %}}

### 2.4 HMAC-secret 扩展

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html)，用于 LUKS 全盘加密

{{% notice note %}}
受 CTAP 实现中的一处 [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) 影响，固件版本小于等于 `1.3` 的 CanoKey 与 libfido2 1.7.0 不兼容，因此不能用于 `systemd-cryptenroll`。
受影响的用户请使用 libfido2 1.6.0。
{{% /notice %}}
