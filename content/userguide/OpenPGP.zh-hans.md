+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/) 是由 [RFC4880](https://tools.ietf.org/html/rfc4880) 规范的签名和加密标准。该标准通过使用私钥来实现信息和文件的签署/加密。常用的 OpenPGP 工具之一是 GNU Privacy Guard，通常简称为 GnuPG 或 GPG。在 Windows 中，还可以使用 [Kleopatra](https://www.openpgp.org/software/kleopatra/)。

## 基本信息

### 支持算法

* RSA2048
* RSA3072
* RSA4096
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* secp256k1

### 默认值

* PIN: 默认为 123456, 最小长度为 6, 最大长度为 64
* Admin PIN: 默认为 12345678, 最小长度为 8, 最大长度为 64
* Reset Code: 默认为空，最小长度为 8, 最大长度为 64
* Signature PIN : forced（每次签名都要验证 PIN）
* 触摸策略：SIG, DEC, AUT 为关闭
* 触摸缓存时间：0

### 触摸策略

{{% notice note %}}
触摸策略仅在使用 USB 接口时生效。
{{% /notice %}}

OpenPGP 最多支持 3 个密钥，即签名密钥 (SIG)、加密密钥 (DEC) 和验证密钥 (AUT)。根据固件版本不同，你可以在 CanoKey Console 中或通过 `gpg` 命令设置 SIG、DEC 和 AUT 的触摸策略。触摸缓存时间的值在 0 至 255 秒之间（0 为无缓存）。

#### 固件版本  <= 1.4

请通过 CanoKey Console 的“设置”应用修改触摸策略。

#### 固件版本  >= 1.5

请通过 GnuPG 修改触摸策略。

### PIN 策略

对于 DEC 和 AUT 密钥，PIN 验证成功后，将不再需要验证，直到断开并重新插入 CanoKey。

对于 SIG，如果 `forcesig` 打开，则每次签名都要求输入 PIN；否则，只在上电后的第一次签名时要求输入 PIN。

## 常用操作

## 常见问题

### GnuPG 和 PC/SC 冲突

GnuPG 默认使用自己的实现（[scdaemon](https://www.gnupg.org/documentation/manuals/gnupg/Invoking-SCDAEMON.html)）访问包括 CanoKey 在内的智能卡，这一实现与 PC/SC 冲突。详情请见：<https://ludovicrousseau.blogspot.com/2019/06/gnupg-and-pcsc-conflicts.html>。

为了避免冲突，我们建议使用 PC/SC 接口访问 CanoKey，即在 `scdaemon.conf` 中增加：

```
disable-ccid
```

Linux 和 macOS 中，这一文件通常位于 `~/.gnupg/scdaemon.conf`。

Windows 中通常不会遇到这一问题，如有必要，请修改 GnuPG 安装目录下的 `scdaemon.conf` 文件。

### PC/SC 占用

由于 PC/SC 对智能卡的访问可能是独占的（取决于应用程序访问模式），因此即使配置正确，GnuPG 仍然可能无法正确访问 CanoKey。如果遇到这一问题，只需重新插拔 CanoKey 即可。

常见占用 PC/SC 的程序包括：

* FireFox：可以在 “Preferences > Privacy&Security > Certificates” 中卸载（unload）“OpenSC Smartcard framework”。
