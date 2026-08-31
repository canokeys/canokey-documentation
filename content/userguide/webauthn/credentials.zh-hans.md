+++
title = "凭据"
date = 2022-06-08T20:30:05+08:00
weight = 2
+++

本页介绍 CanoKey 如何存储和管理 WebAuthn 凭据，以及支持的凭据相关扩展。

## Discoverable Credentials（Resident Keys）

CanoKey 支持 Discoverable Credentials，也称 Resident Keys。

CanoKey 最多可保存 64 个 Discoverable Credentials。

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
