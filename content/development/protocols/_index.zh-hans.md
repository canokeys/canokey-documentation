---
title: "协议"
date: 2019-11-28T10:18:22-05:00
weight: 10
---

CanoKey 支持以下协议：

- FIDO2
- OpenPGP Smart Card 3.4
- PIV (NIST SP 800-73-4)
- OATH
- NDEF
- WebUSB

此外，CanoKey 还提供了一个额外的 Admin 应用（applet）用于管理密钥。

#### FIDO2

实现遵循 [CTAP2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-errata-20220621.html) 和 [CTAP2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html)。

支持的特性：

- 最多 64 个常驻密钥（resident keys）
- HMAC 扩展
- Ed25519

命令列表、GetInfo 内容、支持的算法和扩展以及特定于固件版本的行为，请参阅 [FIDO2 / CTAP2 协议文档](ctap2/)。

#### OpenPGP Smart Card 3.4

CanoKey 实现了[规范](https://gnupg.org/ftp/specs/OpenPGP-smart-card-application-3.4.pdf)中所有的必需特性。此外，还实现了以下可选特性：

- PUT DATA with TAG `C4`
- 算法
  - RSA 2048（卡内生成 / 导入）/ 4096（仅导入）
  - ECDSA 和 ECDH：secp256r1 (NIST P256) / secp384r1 (NIST P384) / secp256k1
  - Ed25519 和 X25519

注意，以下特性不受支持：

- KDF
- Secure Messaging
- AES
- 命令：MANAGE SECURITY ENVIRONMENT

命令列表、数据对象、支持的算法以及特定于固件版本的行为，请参阅 [OpenPGP 应用协议文档](openpgp/)。

#### PIV

CanoKey 实现了[规范](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf)的必需特性，包括生物特征（biometric）和 Security Object 数据对象。它还提供了可配置的算法 ID、PIV attestation、完整的 Retired Key Management 密钥槽等扩展。

命令列表、数据对象、支持的算法以及特定于固件版本的扩展，请参阅 [PIV 应用协议文档](piv/)。

不支持 Secure Messaging。

#### OATH

请参阅 [OATH 文档](oath/)。

#### Admin 应用

请参阅 [Admin 应用文档](admin/)。

#### NDEF

[NFC Forum Type-4 Tag](http://apps4android.org/nfc-specifications/NFCForum-TS-Type-4-Tag_2.0.pdf)。

NDEF 消息的最大容量为 1022 字节。

文件布局、命令以及只读标志，请参阅 [NDEF 应用协议文档](ndef/)。

#### WebUSB

请参阅 [WebUSB 文档](webusb/)。
