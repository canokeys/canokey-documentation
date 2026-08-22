---
title: "PIV 应用"
date: 2026-08-18T00:00:00+08:00
weight: 25
---

CanoKey PIV 应用实现了 [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf) 的必备特性以及一组扩展。本页记录 CanoKey 特有的行为及实现所暴露的命令。标准命令和 TLV 定义请参阅 NIST SP 800-73-4。

除非本节另有说明，本页所记录的扩展均要求固件版本 3.1.1 或更高。

## 1. 应用与传输

### 1.1 AID

PIV 应用标识符为：

```text
A0 00 00 03 08 00 00 10 00 01 00
```

使用 `00 A4 04 00 0B A000000308000010000100` 选择该应用。

### 1.2 APDU 传输

CanoKey 接受短 APDU。扩展长度的命令 APDU 会被拒绝并返回 `6700`。

以下指令支持 ISO 7816-4 命令链（`CLA = 10`）：

- General Authenticate（`INS = 87`）
- Put Data（`INS = DB`）
- Import Asymmetric Key（`INS = FE`）

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
| `F6` | Move/Delete Key | Yubico 兼容扩展 |
| `F7` | Get Metadata | Yubico 兼容扩展 |
| `F8` | Get Serial | Yubico 兼容扩展 |
| `F9` | Attest | Yubico 兼容扩展 |
| `FA` | Set PIN Retries | Yubico 兼容扩展 |
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
| NIST P-521 | `15` | 3.1.1+ |
| ML-DSA-65 | `E2` | 3.1.1+ |
| ML-KEM-768 | `E3` | 3.1.1+ |

固件版本 3.0.0 中，Ed25519 签名仅接受 32 字节输入，且 X25519 仅支持在设备内部生成的密钥。固件版本 3.0.2 及更高版本取消了这些限制。

### 2.2 Algorithm Extension 命令

Algorithm Extension 命令用于读取或写入十字节的扩展算法配置记录。

| 操作 | APDU 头 | 认证 | 数据 |
|:----------|:------------|:---------------|:-----|
| 读取 | `00 EE 01 00` | 无 | 空 |
| 写入 | `00 EE 02 00` | 管理密钥 | 十字节记录 |

记录字段的顺序如下：

| 偏移 | 字段 | 默认值 |
|:------:|:------|:-------:|
| 0 | 扩展算法启用标志 | `01` |
| 1 | Ed25519 ID | `E0` |
| 2 | RSA 3072 ID | `05` |
| 3 | RSA 4096 ID | `16` |
| 4 | X25519 ID | `E1` |
| 5 | secp256k1 ID | `53` |
| 6 | NIST P-521 ID | `15` |
| 7 | SM2 ID | `54` |
| 8 | ML-DSA-65 ID | `E2` |
| 9 | ML-KEM-768 ID | `E3` |

启用字段必须为 `00` 或 `01`。算法 ID 可以使用任意字节值，且允许重复。写入成功后立即生效。

## 3. 密钥槽与策略

| 密钥槽 | 用途 | 默认 PIN 策略 | 默认触摸策略 |
|:----:|:--------|:-------------------|:---------------------|
| `9A` | PIV Authentication | 一次 | 从不 |
| `9C` | Digital Signature | 总是 | 从不 |
| `9D` | Key Management | 一次 | 从不 |
| `9E` | Card Authentication | 从不 | 从不 |
| `82`-`95` | Retired Key Management | 一次 | 从不 |
| `F9` | Attestation | 不适用 | 不适用 |

固件版本 2.0.0 支持 Retired Key Management 槽位 `82` 和 `83`。固件版本 3.1.1 支持完整的 `82` 至 `95` 范围；这些槽位的存储空间仅在使用时分配。

设备证明槽位仅接受 P-256 密钥。其密钥和证书单独写入，重置 PIV 应用时会被保留。

PIN 策略取值为「从不」「一次」和「总是」。触摸策略取值为「从不」「总是」和「缓存」；缓存时长为 15 秒。触摸要求仅在 USB 连接时生效，NFC 连接时不强制执行。

## 4. 数据对象

Get Data 和 Put Data 使用 NIST SP 800-73-4 定义的标准 `5C` 标签列表和 `53` 数据容器。所有可写对象都需要管理密钥认证。可选对象仅在写入后分配空间。

| 标签 | 数据对象 | 容量 | 读取权限 |
|:---:|:------------|---------:|:------------|
| `7E` | Discovery Object | 合成 | 公开，只读 |
| `7F61` | Biometric Information Templates Group Template | 合成 | 公开，只读 |
| `5FC101` | Card Authentication Certificate | 6144 字节 | 公开 |
| `5FC102` | Cardholder Unique Identifier | 2916 字节 | 公开 |
| `5FC103` | Cardholder Fingerprints | 512 字节 | PIN |
| `5FC105` | PIV Authentication Certificate | 6144 字节 | 公开 |
| `5FC106` | Security Object | 245 字节 | 公开 |
| `5FC107` | Card Capability Container | 287 字节 | 公开 |
| `5FC108` | Cardholder Facial Image | 512 字节 | PIN |
| `5FC109` | Printed Information | 245 字节 | PIN |
| `5FC10A` | Digital Signature Certificate | 6144 字节 | 公开 |
| `5FC10B` | Key Management Certificate | 6144 字节 | 公开 |
| `5FC10C` | Key History Object | 32 字节 | 公开 |
| `5FC10D`-`5FC120` | Retired Key Management Certificates | 各 6144 字节 | 公开 |
| `5FC121` | Cardholder Iris Images | 512 字节 | PIN |
| `5FFF00` | Pairing Code Reference Data / Admin Data | 128 字节 | 公开 |
| `5FFF01` | Attestation Certificate | 6144 字节 | 公开 |

`5FFF01` 设备证明证书对象在 PIV 重置后会被保留。其他可写数据对象会被清除。

证书容量在固件版本 3.1.1 及更高版本为 6144 字节，在固件版本 1.6 至 3.0.x 为 3000 字节，在固件版本 1.5 及更早版本为 1000 字节。

## 5. 固件 3.1.1 扩展

### 5.1 AES-192 管理密钥

管理密钥长度为 24 字节，使用 AES-192，算法 ID 为 `0A`。默认值为：

```text
01 02 03 04 05 06 07 08 01 02 03 04 05 06 07 08
01 02 03 04 05 06 07 08
```

Set Management Key 使用 Yubico 兼容的数据形式 `0A 9B 18 <24-byte-key>`。`P2 = FF` 表示管理密钥认证不要求触摸；`P2 = FE` 表示要求触摸。

### 5.2 Set PIN Retries

使用以下命令设置 PIN 和 PUK 的重试次数上限：

```text
00 FA <pin-retries> <puk-retries>
```

该命令没有数据字段。两个重试次数值都必须在 1 到 15 之间。发送该命令前需要同时完成管理密钥认证和 PIN 验证。

命令成功执行后，PIN 会被重置为 `123456`，PUK 会被重置为 `12345678`，并采用所请求的重试次数上限。

### 5.3 移动或删除密钥

使用以下命令在槽位之间移动密钥：

```text
00 F6 <destination-slot> <source-slot>
```

将目标槽位设为 `FF` 即可删除密钥：

```text
00 F6 FF <source-slot>
```

这两个命令都没有数据字段，且需要管理密钥认证。目标槽位必须为空。这些操作只影响私钥及其元数据；证书数据对象不会被移动或删除。

### 5.4 密钥设备证明

使用以下命令请求设备证明证书：

```text
00 F9 <slot> 00
```

该命令没有数据字段，既不需要 PIN 验证，也不需要管理密钥认证。它返回由 `F9` 槽位中的 P-256 密钥签名的 DER 编码 X.509 证书。

只有在设备上生成的密钥才能进行设备证明。导入的密钥会被拒绝。使用该命令前，必须先写入 `F9` 槽位中的设备证明密钥以及数据对象 `5FFF01` 中的签发者证书；这两项数据在 PIV 重置后仍然保留。

## 6. 其他扩展命令

### 6.1 Get Metadata

使用 `00 F7 00 <reference>`，无数据字段。引用可以是 PIN（`80`）、PUK（`81`）、管理密钥（`9B`）或受支持的非对称密钥槽。响应遵循 Yubico PIV 元数据 TLV 格式，在适用时报告算法、策略、来源、公钥、默认状态和重试计数器等值。

使用 `00 F7 01 00`（无数据字段）可获取紧凑的元数据目录，无需认证即可在单次响应中列出各密钥槽中是否存在密钥或证书。响应包含两个 TLV：

```text
01 01 <version>  02 <length>  <entries>
```

标签 `01` 携带一字节目录版本（当前为 `01`）。标签 `02` 携带条目载荷，每个槽占一个六字节的条目：

| 字节 | 内容 |
|:----:|:-----|
| 0 | 槽 ID |
| 1 | 标志位：`01` 位表示存在密钥，`02` 位表示存在证书 |
| 2 | 算法 ID；无密钥时为 `00` |
| 3 | 来源（`01` 设备生成，`02` 导入）；无密钥时为 `00` |
| 4 | PIN 策略；无密钥时为 `00` |
| 5 | 触摸策略；无密钥时为 `00` |

槽按 `9A`、`9C`、`9D`、`9E`、`82` 至 `95` 的顺序枚举；既无密钥也无证书的槽会被省略，因此载荷最多包含 24 个条目。

### 6.2 Get Serial 和 Get Version

- `00 F8 00 00` 返回四字节设备序列号。
- `00 FD 00 00` 返回三字节 PIV 应用版本。

### 6.3 Import Asymmetric Key

使用 `INS = FE`，算法 ID 放在 `P1` 中，目标槽位放在 `P2` 中。需要管理密钥认证。对于无法放入一个短 APDU 的密钥材料，该命令支持 ISO 7816-4 命令链。导入密钥的格式遵循 Yubico PIV TLV 格式。

### 6.4 Reset

使用 `00 FB 00 00`，无数据字段。仅当 PIN 和 PUK 均被锁定时才接受 Reset。它会恢复 PIV 用户数据和默认值，同时保留 `F9` 槽位中的设备证明密钥和数据对象 `5FFF01` 中的设备证明证书。
