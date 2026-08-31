---
title: "PIV 应用"
date: 2026-08-18T00:00:00+08:00
weight: 25
---

CanoKey PIV 应用实现了 [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf) 的必备特性以及一组扩展。本页记录 CanoKey 特有的行为及实现所暴露的命令。标准命令和 TLV 定义请参阅 NIST SP 800-73-4。

## 1. 应用与传输

### 1.1 AID

PIV 应用标识符为：

```text
A0 00 00 03 08 00 00 10 00 01 00
```

使用 `00 A4 04 00 0B A000000308000010000100` 选择该应用。

### 1.2 APDU 传输

CanoKey 接受短 APDU。扩展长度的命令 APDU 会被拒绝并返回 `6700`。

当响应数据无法在一次响应中返回时，CanoKey 返回 `61xx`。使用 Get Response（`INS = C0`）获取剩余数据。

### 1.3 指令

| INS | 名称 | 定义 |
|:---:|:-----|:-----------|
| `20` | Verify | NIST SP 800-73-4 |
| `24` | Change Reference Data | NIST SP 800-73-4 |
| `2C` | Reset Retry Counter | NIST SP 800-73-4 |
| `47` | Generate Asymmetric Key Pair | NIST SP 800-73-4 |
| `87` | General Authenticate | NIST SP 800-73-4 |
| `A4` | Select | ISO 7816-4 |
| `C0` | Get Response | ISO 7816-4 |
| `CB` | Get Data | NIST SP 800-73-4 |
| `DB` | Put Data | NIST SP 800-73-4 |
| `EE` | Algorithm Extension | CanoKey 扩展 |
| `F7` | Get Metadata | Yubico 兼容扩展 |
| `F8` | Get Serial | Yubico 兼容扩展 |
| `FB` | Reset | Yubico 兼容扩展 |
| `FD` | Get Version | Yubico 兼容扩展 |
| `FE` | Import Asymmetric Key | Yubico 兼容扩展 |
| `FF` | Set Management Key | Yubico 兼容扩展 |

## 2. 算法

### 2.1 算法 ID

标准算法的 ID 是固定的。扩展算法的 ID 可配置，当其取值影响互操作性时，必须从设备读取。

| 算法 | 默认 ID | 可用性 |
|:----------|:----------:|:-------------|
| RSA 2048 | `07` | 所有受支持的固件版本 |
| NIST P-256 | `11` | 所有受支持的固件版本 |
| NIST P-384 | `14` | 所有受支持的固件版本 |
| RSA 3072 | `05` | 3.0.0+ |
| RSA 4096 | `16` | 3.0.0+ |
| Ed25519 | `E0` | 3.0.0+ |
| X25519 | `E1` | 3.0.0+ |
| secp256k1 | `53` | 3.0.0+ |
| SM2 | `54` | 3.0.0+ |

固件版本 3.0.0 中，Ed25519 签名仅接受 32 字节输入，且 X25519 仅支持在设备内部生成的密钥。固件版本 3.0.2 及更高版本取消了这些限制。

### 2.2 Algorithm Extension 命令

Algorithm Extension 命令用于读取或写入七字节的扩展算法配置记录。

| 操作 | APDU 头 | 认证 | 数据 |
|:----------|:------------|:---------------|:-----|
| 读取 | `00 EE 01 00` | 无 | 空 |
| 写入 | `00 EE 02 00` | 管理密钥 | 七字节记录 |

记录字段的顺序如下：

| 偏移 | 字段 | 默认值 |
|:------:|:------|:-------:|
| 0 | 扩展算法启用标志 | `01` |
| 1 | Ed25519 ID | `E0` |
| 2 | RSA 3072 ID | `05` |
| 3 | RSA 4096 ID | `16` |
| 4 | X25519 ID | `E1` |
| 5 | secp256k1 ID | `53` |
| 6 | SM2 ID | `54` |

启用字段必须为 `00` 或 `01`。算法 ID 可以使用任意字节值，且允许重复。写入成功后立即生效。

## 3. 密钥槽与策略

| 密钥槽 | 用途 | 默认 PIN 策略 | 默认触摸策略 |
|:----:|:--------|:-------------------|:---------------------|
| `9A` | PIV Authentication | 一次 | 从不 |
| `9C` | Digital Signature | 一次 | 从不 |
| `9D` | Key Management | 一次 | 从不 |
| `9E` | Card Authentication | 从不 | 从不 |
| `82`-`83` | Retired Key Management | 一次 | 从不 |

固件版本 2.0.0 及更高版本支持 Retired Key Management 槽位 `82` 和 `83`。

PIN 策略取值为「从不」「一次」和「总是」。触摸策略取值为「从不」「总是」和「缓存」；缓存时长为 15 秒。触摸要求仅在 USB 连接时生效，NFC 连接时不强制执行。

## 4. 数据对象

Get Data 和 Put Data 使用 NIST SP 800-73-4 定义的标准 `5C` 标签列表和 `53` 数据容器。所有可写对象都需要管理密钥认证。

| 标签 | 数据对象 | 容量 |
|:---:|:------------|---------:|
| `7E` | Discovery Object | 合成 |
| `5FC101` | Card Authentication Certificate | 3000 字节 |
| `5FC102` | Cardholder Unique Identifier | 2916 字节 |
| `5FC105` | PIV Authentication Certificate | 3000 字节 |
| `5FC107` | Card Capability Container | 287 字节 |
| `5FC109` | Printed Information | 245 字节 |
| `5FC10A` | Digital Signature Certificate | 3000 字节 |
| `5FC10B` | Key Management Certificate | 3000 字节 |
| `5FC10D`-`5FC10E` | Retired Key Management Certificates | 各 3000 字节 |
证书容量在固件版本 1.6 及更高版本为 3000 字节，在固件版本 1.5 及更早版本为 1000 字节。

## 5. 其他扩展命令

### 5.1 Get Metadata

使用 `00 F7 00 <reference>`，无数据字段。引用可以是 PIN（`80`）、PUK（`81`）、管理密钥（`9B`）或受支持的非对称密钥槽。响应遵循 Yubico PIV 元数据 TLV 格式，在适用时报告算法、策略、来源、公钥、默认状态和重试计数器等值。

### 5.2 Get Serial 和 Get Version

- `00 F8 00 00` 返回四字节设备序列号。
- `00 FD 00 00` 返回三字节 PIV 应用版本。

### 5.3 Import Asymmetric Key

使用 `INS = FE`，算法 ID 放在 `P1` 中，目标槽位放在 `P2` 中。需要管理密钥认证。导入密钥的格式遵循 Yubico PIV TLV 格式。

### 5.4 Reset

使用 `00 FB 00 00`，无数据字段。仅当 PIN 和 PUK 均被锁定时才接受 Reset。它会恢复 PIV 用户数据和默认值。
