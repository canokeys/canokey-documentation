---
title: "FIDO2 / CTAP2"
date: 2026-08-21T00:00:00+08:00
weight: 5
---

CanoKey 的 FIDO2 应用实现了 Client to Authenticator Protocol（CTAP）。根据固件版本不同，它符合 [CTAP 2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) 或 [CTAP 2.1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-errata-20220621.html)，同时也实现了 U2F（CTAP1）协议。本页介绍所支持的命令、扩展、算法以及各固件版本特有的行为。

## 1. 传输接口与版本支持

该应用可通过 USB（CTAPHID）和 NFC 使用。在 ISO 7816 接口（USB CCID 或 NFC）上，使用 AID `A0 00 00 06 47 2F 00 01` 选择 FIDO 应用；随后以 `CLA = 80, INS = 10` 的 APDU 携带 CBOR 消息发送 CTAP2 命令，U2F 命令则使用 `CLA = 00`（见第 5 节）。

| CTAP 版本 | 固件要求 |
|:-------------|:---------------------|
| CTAP 2.0（`FIDO_2_0`） | 所有固件版本 |
| CTAP 2.1（`FIDO_2_1`） | 2.0.0 及更高版本 |

除不支持 U2F 的固件版本外，`U2F_V2` 也会出现在版本列表中（见第 5 节）。

## 2. 命令

| 命令 | 代码 | 可用性 |
|:--------|:----:|:-------------|
| authenticatorMakeCredential | `01` | 所有固件版本 |
| authenticatorGetAssertion | `02` | 所有固件版本 |
| authenticatorGetInfo | `04` | 所有固件版本 |
| authenticatorClientPIN | `06` | 所有固件版本 |
| authenticatorReset | `07` | 所有固件版本 |
| authenticatorGetNextAssertion | `08` | 所有固件版本 |
| authenticatorCredentialManagement | `0A` | 2.0.0 及更高版本（兼容旧别名 `41`） |
| authenticatorSelection | `0B` | 所有固件版本 |
| authenticatorLargeBlobs | `0C` | 2.0.0 及更高版本 |

authenticatorCredentialManagement 支持以下子命令：getCredsMetadata、enumerateRPsBegin、enumerateRPsGetNextRP、enumerateCredentialsBegin、enumerateCredentialsGetNextCredential、deleteCredential 和 updateUserInformation。

authenticatorReset 仅在上电后 10 秒内被接受，并且要求用户在场（一次触摸）。

## 3. authenticatorGetInfo

固件 2.0.0 及更高版本报告以下字段。标记为“动态”的项取决于设备状态。

| 键 | 字段 | 值 |
|:---:|:------|:------|
| `01` | versions | `U2F_V2`（支持时）、`FIDO_2_0`、`FIDO_2_1` |
| `02` | extensions | `credBlob`、`credProtect`、`hmac-secret`、`largeBlobKey` |
| `03` | aaguid | `244eb29e-e090-4e49-81fe-1f20f8d3b8f4` |
| `04` | options | `rk`、`credMgmt`、`largeBlobs`、`pinUvAuthToken`：true；`clientPin`、`makeCredUvNotRqd`：动态 |
| `05` | maxMsgSize | 取决于设备 |
| `06` | pinUvAuthProtocols | `[1, 2]` |
| `07` | maxCredentialCountInList | 16 |
| `08` | maxCredentialIDLength | 70 |
| `09` | transports | `nfc`、`usb` |
| `0A` | algorithms | 见第 4 节 |
| `0B` | maxSerializedLargeBlobArray | 4096 |
| `0F` | maxCredBlobLength | 32 |
| `16` | attestationFormats | `packed` |

更早的固件版本报告其中的一个子集：固件 1.x 列出版本 `FIDO_2_0` 和 `U2F_V2`、`hmac-secret` 扩展、PIN 协议 1，以及 `rk` 和 `clientPin` 选项。固件 2.0.0 增加了 `FIDO_2_1`、PIN 协议 2、`credProtect`、`credBlob` 和 `largeBlobKey` 扩展，以及 `credMgmt`、`largeBlobs`、`pinUvAuthToken` 和 `makeCredUvNotRqd` 选项。

## 4. 算法

| 算法 | COSE alg | 可用性 |
|:----------|:--------:|:-------------|
| ES256（基于 NIST P-256 的 ECDSA，使用 SHA-256） | `-7` | 所有固件版本 |
| EdDSA（Ed25519） | `-8` | 所有固件版本 |
| SM2 | `-48` | 3.0.0 及更高版本 |

SM2 的算法标识符和曲线标识符可通过 Admin 应用（指令 `11h` / `12h`）由厂商配置。在固件 3.0.x 中，SM2 默认禁用，仅在启用后才会出现在算法列表中，默认算法 ID 为 `-48`。

## 5. U2F（CTAP1）

U2F 命令使用 `CLA = 00`：Register（`INS = 01`）、Authenticate（`INS = 02`，`P1 = 03` 强制要求用户在场，`P1 = 07` 为仅检查模式）和 Version（`INS = 03`，返回 `U2F_V2`）。U2F 凭据是 ES256 密钥句柄，与 CTAP2 的 non-discoverable 凭据兼容。

固件 3.0.0 不支持 U2F；固件 3.0.2 及更高版本恢复了支持。

## 6. Discoverable Credentials（Resident Keys）

所有固件版本都支持 Discoverable Credentials（Resident Keys）。CanoKey 最多可保存 64 个 Discoverable Credentials。

## 7. PIN

默认未设置 PIN。支持 PIN 协议 1 和 2（协议 2 自固件 2.0.0 起支持）。PIN 重试计数器为 8。authenticatorClientPIN 支持由所实现的 CTAP 版本定义的子命令，包括自固件 2.0.0 起基于权限的 pinUvAuthToken（`getPinUvAuthTokenUsingPinWithPermissions`）。

认证（attestation）使用 `packed` 格式以及设备密钥和证书；attestation 密钥和证书可通过 Admin 应用写入（参见 [Admin 应用文档](../admin/)）。
