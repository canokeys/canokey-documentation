+++
title = "SSH"
date = 2022-06-08T20:30:05+08:00
weight = 4
+++

CanoKey 可以通过 FIDO 密钥用于 SSH 认证。

## OpenSSH 版本要求

为了使用 FIDO 密钥进行 SSH 身份验证，需要确保安装的 OpenSSH 版本支持该功能。最低版本要求如下：
- OpenSSH 8.2 及以上版本

您可以使用以下命令检查当前的 `ssh` 客户端和 `sshd` 服务版本：

```sh
ssh -V
sshd -V
```

## 使用 ECDSA-SK 和 ED25519-SK 密钥对

### 创建 ECDSA-SK 和 ED25519-SK 密钥对

1. 创建一个 ECDSA-SK 密钥对：

```sh
ssh-keygen -t ecdsa-sk -f ~/.ssh/id_ecdsa_sk
```

2. 创建一个 ED25519-SK 密钥对：

```sh
ssh-keygen -t ed25519-sk -f ~/.ssh/id_ed25519_sk
```

### 将公钥添加到目标服务器

将生成的公钥文件（`~/.ssh/id_ecdsa_sk.pub` 或者 `~/.ssh/id_ed25519_sk.pub`）的内容添加到目标服务器的 `~/.ssh/authorized_keys` 文件中。

您可以使用以下命令将公钥复制到远程服务器：

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

或者

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```

### 在其他机器上使用

将生成的私钥文件（`~/.ssh/id_ecdsa_sk` 或者 `~/.ssh/id_ed25519_sk`）复制到需要使用的其他机器上。确保文件权限正确：

```sh
chmod 600 ~/.ssh/id_ecdsa_sk
chmod 600 ~/.ssh/id_ed25519_sk
```

## 使用 Discoverable Credential（Resident Key）

{{% notice note %}}
CanoKey 固件版本最低要求为 2.0.0。
{{% /notice %}}

### 创建 RK 密钥

1. 创建一个 ECDSA-SK RK：

```sh
ssh-keygen -t ecdsa-sk -O resident -f ~/.ssh/id_ecdsa_sk
```

2. 创建一个 ED25519-SK RK：

```sh
ssh-keygen -t ed25519-sk -O resident -f ~/.ssh/id_ed25519_sk
```

### 将公钥添加到目标服务器

与上述非 RK 密钥相同，将生成的公钥文件内容添加到服务器的 `~/.ssh/authorized_keys` 文件中。

```sh
ssh-copy-id -i ~/.ssh/id_ecdsa_sk.pub username@remote_host
```

或者

```sh
ssh-copy-id -i ~/.ssh/id_ed25519_sk.pub username@remote_host
```
