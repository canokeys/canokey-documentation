+++
title = "OpenPGP"
date = 2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/) 是由 [RFC4880](https://tools.ietf.org/html/rfc4880) 规范的签名和加密标准。该标准通过使用私钥来实现信息和文件的签署/加密。常用的 OpenPGP 工具之一是 GNU Privacy Guard，通常简称为 GnuPG 或 GPG。在 Windows 中，还可以使用 [Kleopatra](https://www.openpgp.org/software/kleopatra/)。

CanoKey 实现了 OpenPGP Card 3.4 规范，最多可容纳 3 个密钥，即签名密钥 (SIG)、加密密钥 (DEC) 和验证密钥 (AUT)。

## 支持算法

* RSA2048
* RSA3072
* RSA4096
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* NIST P-521（secp521r1）
* secp256k1
* SM2

{{% notice note %}}
固件版本 1.6.1 及之前仅支持 e = 65537 的 RSA 公钥。
固件版本 2.0.0 起支持 RSA3072 / RSA4096 密钥生成。
固件版本 3.1.1 起支持 NIST P-521 算法。
{{% /notice %}}

## 本章内容

* [PIN 和触摸策略](pin-and-touch-policies/) —— 默认值、PIN 和触摸策略的语义，以及不同固件版本下的配置方法。
* [常用操作](usage/) —— 日常 GnuPG 工作流：查看卡片、生成或导入密钥、修改 PIN。
* [常见问题](faq/) —— 排查 GnuPG 与 PC/SC 的冲突问题。
