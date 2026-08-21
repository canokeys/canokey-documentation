+++
title = "WebAuthn (Passkey)"
date = 2022-06-08T20:30:05+08:00
weight = 15
+++

CanoKey 通过 CTAP 规范支持 WebAuthn（Passkey）认证协议。

## CTAP 版本支持

| CTAP 版本 | 固件要求 |
| --- | --- |
| CTAP 2.0 | 所有版本 |
| CTAP 2.1 | 固件 2.0.0 及更高版本 |
| CTAP 2.3 | 固件 3.1.1 及更高版本 |

## 特性

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

## 多因素认证

CanoKey 可以用于很多[网站](https://2fa.directory/int/)的双因素认证。

{{% notice note %}}
CanoKey 默认不设置 PIN，部分网站及部分功能（如 Discoverable Credentials 管理）要求您必须设置 PIN，请在收到提示时设置。
{{% /notice %}}

## 本章内容

* [PIN 与配置](pin-and-config/) — PIN 设置、PIN Protocol 2 以及固件 3.1.1 的配置选项。
* [凭据](credentials/) — Discoverable Credentials、凭据管理及相关扩展。
* [重置](reset/) — 重置 WebAuthn 应用及其影响。
* [SSH](ssh/) — 使用 FIDO 密钥（`ecdsa-sk` / `ed25519-sk`）进行 OpenSSH 认证。
* [PAM](pam/) — 通过 pam-u2f 进行 PAM 认证。
* [HMAC-secret 扩展](hmac-secret/) — 使用 systemd-cryptenroll 进行 LUKS 全盘加密。
