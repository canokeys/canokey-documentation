+++
title = "凭据"
date = 2022-06-08T20:30:05+08:00
weight = 2
+++

本页介绍 CanoKey 如何存储和管理 WebAuthn 凭据，以及支持的凭据相关扩展。

## Discoverable Credentials（Resident Keys）

CanoKey 支持 Discoverable Credentials，也称 Resident Keys。

在早于 3.1.1 的固件版本中，Discoverable Credentials 的数量固定为最多 64 个。在固件版本 3.1.1 及更高版本中，数量不再固定为 64 个，实际容量取决于设备的可用存储空间；设备还会报告当前剩余容量。

## 凭据管理

固件版本 2.0.0 及更高版本支持 Discoverable Credentials 管理。

{{% notice note %}}
凭据管理要求先设置 PIN。
{{% /notice %}}

## 凭据相关扩展

| 扩展 | 固件要求 |
| --- | --- |
| HMAC 扩展（`hmac-secret`） | 所有版本 |
| `credProtect` | 固件 2.0.0 及更高版本 |
| `credBlob` | 固件 2.0.0 及更高版本 |
| `largeBlobKey` | 固件 2.0.0 及更高版本 |
| Large Blob | 固件 2.0.0 及更高版本 |
| `minPinLength` | 固件 3.1.1 及更高版本 |
| `thirdPartyPayment` | 固件 3.1.1 及更高版本 |
| `hmac-secret-mc`（创建凭据时的 HMAC-secret） | 固件 3.1.1 及更高版本 |
