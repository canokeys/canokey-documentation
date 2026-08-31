---
title: "OpenPGP 应用"
date: 2026-08-21T00:00:00+08:00
weight: 20
---

CanoKey OpenPGP 应用实现了 [OpenPGP 智能卡应用规范 3.4 版](https://gnupg.org/ftp/specs/OpenPGP-smart-card-application-3.4.pdf)的全部必备特性，以及一组可选和厂商特有的特性。本页记录实现所暴露的命令及 CanoKey 特有的行为。标准命令和数据对象定义请参阅该规范。

## 1. 应用与传输

### 1.1 AID

OpenPGP 应用标识符前缀为：

```text
D2 76 00 01 24 01
```

使用 `00 A4 04 00 06 D27600012401` 选择该应用。Application Related Data 中返回的完整 AID 还会附加版本字节 `03 04`、厂商 ID `F1 D0`、四字节设备序列号以及两个字节 `00`。

### 1.2 APDU 传输

CanoKey 接受短 APDU。命令使用 `CLA = 00`。

当响应数据无法在一次响应中返回时，CanoKey 返回 `61xx`。使用 Get Response（`INS = C0`）获取剩余数据。

### 1.3 指令

| INS | 名称 | 定义 |
|:---:|:-----|:-----------|
| `20` | Verify | OpenPGP 卡规范 |
| `24` | Change Reference Data | OpenPGP 卡规范 |
| `2A` | PSO: Compute Digital Signature / Decipher | OpenPGP 卡规范 |
| `2C` | Reset Retry Counter | OpenPGP 卡规范 |
| `44` | Activate File | OpenPGP 卡规范 |
| `47` | Generate Asymmetric Key Pair | OpenPGP 卡规范 |
| `84` | Get Challenge | OpenPGP 卡规范 |
| `88` | Internal Authenticate | OpenPGP 卡规范 |
| `A4` | Select | ISO 7816-4 |
| `A5` | Select Data | OpenPGP 卡规范 |
| `C0` | Get Response | ISO 7816-4 |
| `CA` | Get Data | OpenPGP 卡规范 |
| `CC` | Get Next Data | OpenPGP 卡规范 |
| `DA` | Put Data | OpenPGP 卡规范 |
| `DB` | Import Key | OpenPGP 卡规范 |
| `E6` | Terminate DF | OpenPGP 卡规范 |

该规范的以下可选特性不受支持：KDF、Secure Messaging、AES，以及 Manage Security Environment 命令。

## 2. PIN 与重试计数器

该应用使用三个密码：用户 PIN（PW1，引用 `81` 和 `82`）、Admin PIN（PW3，引用 `83`），以及可选的 Reset Code（RC）。

| 项目 | 默认值 | 最小长度 | 最大长度 | 默认重试次数 |
|:-----|:-------:|:--------------:|:--------------:|:---------------:|
| PIN (PW1) | `123456` | 6 | 64 | 3 |
| Admin PIN (PW3) | `12345678` | 8 | 64 | 3 |
| Reset Code (RC) | 未设置 | 8 | 64 | 3 |

Verify 使用 `P1 = 00`，`P2 = 81`（用于签名的 PW1）、`P2 = 82`（用于解密和认证的 PW1）或 `P2 = 83`（PW3）。`P1 = FF` 重置相应的验证状态。空数据字段用于查询状态而不执行验证：若已验证，应用返回 `9000`；否则返回 `63Cx` 并附带剩余重试次数，或在锁定时返回 `6983`。

PW Status 数据对象（`C4`）的第一个字节控制以引用 `81` 验证的 PW1 是被每次签名消耗（`00`，出厂默认：每次 PSO 签名都需要验证）还是被保留（`01`）。

Reset Retry Counter 接受 `P1 = 00`（数据字段中为 Reset Code；未设置 Reset Code 时会被拒绝并返回 `6982`），或 `P1 = 02`（需先验证 Admin PIN）。两种情况下，数据字段中随后都应跟上新的 PIN。

## 3. 密钥与算法

### 3.1 密钥槽

共有三个密钥槽，由 Generate Asymmetric Key Pair 和 Import Key 数据字段中的控制引用模板（CRT）标识：

| CRT 标签 | 密钥槽 | 密钥引用 |
|:-------:|:-----|:-------------:|
| `B6` | Signature (SIG) | `01` |
| `B8` | Decryption (DEC) | `02` |
| `A4` | Authentication (AUT) | `03` |

### 3.2 算法属性

算法属性数据对象 `C1`（SIG）、`C2`（DEC）和 `C3`（AUT）可在验证 Admin PIN 后写入。更改某个槽位的属性会删除其中存储的密钥。DEC 槽位不接受 Ed25519；SIG 和 AUT 槽位不接受 X25519。

| 算法 | 属性格式 | 可用性 |
|:----------|:------------------|:-------------|
| RSA 2048 / 3072 / 4096 | `01` + 模数位宽（2 字节）+ 指数位宽（2 字节，`0020`） | RSA 2048：所有固件版本；RSA 3072 / 4096 生成：2.0.0+ |
| ECDSA / ECDH NIST P-256 | `13` / `12` + OID `2A 86 48 CE 3D 03 01 07` | 所有受支持的固件版本 |
| ECDSA / ECDH secp256k1 | `13` / `12` + OID `2B 81 04 00 0A` | 所有受支持的固件版本 |
| ECDSA / ECDH NIST P-384 | `13` / `12` + OID `2B 81 04 00 22` | 所有受支持的固件版本 |
| Ed25519 (SIG, AUT) | `16` + OID `2B 06 01 04 01 DA 47 0F 01` | 所有受支持的固件版本 |
| X25519 (DEC) | `12` + OID `2B 06 01 04 01 97 55 01 05 01` | 所有受支持的固件版本 |
| SM2 | `13` / `12` + OID `06 08 2A 81 1C CF 55 01 82 2D` | 2.0.0+ |

固件版本 1.6.1 及更早版本仅支持 e = 65537 的 RSA 公钥；这些版本上的卡内 RSA 生成仅限 RSA 2048（RSA 4096 只能导入）。固件版本 2.0.0 及更高版本支持生成 RSA 3072 / 4096 密钥。生成的 RSA 密钥始终使用 e = 65537。

### 3.3 Generate Asymmetric Key Pair

```text
00 47 <P1> 00 <Lc> <CRT>
```

`P1 = 80` 在由数据字段中 CRT 标签选定的槽位生成新密钥（`B6 00`、`B8 00` 或 `A4 00`；也接受五字节形式，如 `B6 03 84 01 01`）。`P1 = 81` 读取已有密钥的公钥，若槽位为空则失败并返回 `6A88`。响应为 `7F49` 公钥数据对象。生成新的 SIG 密钥会将数字签名计数器清零。

与 Put Data 和 Import Key 不同，该命令不需要验证 Admin PIN。

### 3.4 PSO 与 Internal Authenticate

- PSO Compute Digital Signature：`00 2A 9E 9A`。要求以引用 `81` 验证 PW1。对于 RSA 密钥，DigestInfo 输入不得超过模数长度的 40%；对于 ECDSA 密钥，摘要会在左侧补零至私钥长度。每次成功签名都会使数字签名计数器（Security Support Template `7A` 内的数据对象 `93`）加一。
- PSO Decipher：`00 2A 80 86`。要求以引用 `82` 验证 PW1。对于 RSA 密钥，数据字段以填充指示字节 `00` 开头，其后是密文；结果中的 RSA PKCS#1 v1.5 填充会被移除。对于 ECDH 密钥，数据字段为 Cipher DO `A6`，其中包含 Public Key DO `7F49`，外部公钥放在标签 `86` 中（短 Weierstrass 曲线为 `04 || x || y`，X25519 为 `x`）；返回共享秘密。
- Internal Authenticate：`00 88 00 00`。使用 AUT 密钥，要求以引用 `82` 验证 PW1。输入处理方式与 PSO Compute Digital Signature 相同。

### 3.5 Import Key

```text
00 DB 3F FF <Lc> <extended header list>
```

需要验证 Admin PIN。数据字段为扩展头列表 `4D`，其中包含控制引用模板（`B6`、`B8` 或 `A4`），其后是规范定义的私钥模板（`7F48` 和 `5F48`）。导入前必须先设置目标槽位的算法属性。导入 SIG 密钥会将数字签名计数器清零。

### 3.6 Get Challenge

`00 84 00 00 <Le>` 返回 `Le` 个随机字节。

## 4. 数据对象

### 4.1 Get Data

Get Data 使用 `INS = CA`，标签放在 `P1:P2` 中，数据字段为空。可直接读取的标签：

| 标签 | 数据对象 | 备注 |
|:---:|:------------|:------|
| `4F` | AID | 包含设备序列号的完整 AID |
| `5E` | Login data | 最长 63 字节 |
| `5F50` | URL | 最长 255 字节 |
| `5F52` | Historical bytes | 常量 |
| `65` | Cardholder Related Data | 包含姓名 `5B`（39 字节）、语言 `5F2D`（8 字节）、性别 `5F35`（1 字节） |
| `6E` | Application Related Data | 构造生成；见下文 |
| `7A` | Security Support Template | 包含数字签名计数器 `93` |
| `7F21` | Cardholder certificate | 最长 1152 字节；见第 4.3 节 |
| `7F66` | Extended Length Info | 常量 |
| `7F74` | General Feature Management | 声明提供用于用户确认的按键 |
| `C4` | PW Status | PIN 策略字节、长度和重试计数器 |
| `DE` | Key Info | 密钥引用及其来源（不存在 / 生成 / 导入） |
| `FA` | Algorithm Information | 各槽位支持的算法属性 |
| `0102` | 触摸缓存时间 | CanoKey 扩展；一个字节，单位为秒 |

Application Related Data `6E` 包含 AID `4F`、Historical Bytes `5F52`、Extended Length Info `7F66`、General Feature Management `7F74`，以及 Discretionary Data Objects `73`（Extended Capabilities `C0`、Algorithm Attributes `C1`-`C3`、PW Status `C4`、Fingerprints `C5`、CA Fingerprints `C6`、Key Generation Dates `CD`、Key Info `DE` 和 UIF 对象 `D6`-`D8`）。

### 4.2 Put Data

Put Data 使用 `INS = DA`，标签放在 `P1:P2` 中。所有写入都需要验证 Admin PIN。可写标签：`5B`、`5E`、`5F2D`、`5F35`、`5F50`、`7F21`、`C1`-`C3`（算法属性）、`C4`（仅第一个字节，`00` 或 `01`）、`C7`-`C9`（密钥指纹）、`CA`-`CC`（CA 指纹）、`CE`-`D0`（密钥生成日期）、`D3`（Reset Code；空数据字段将其移除）、`D6`-`D8`（UIF），以及 `0102`（触摸缓存时间）。

### 4.3 证书实例

Select Data（`INS = A5`，`P1 = 00`-`02`，`P2 = 04`，数据 `60 04 5C 02 7F 21`）选择后续对 `7F21` 执行 Get Data 或 Put Data 时所用的 SIG、DEC 或 AUT 证书实例。在对 `7F21` 执行 Get Data 之后，Get Next Data（`INS = CC`，`P1:P2 = 7F21`）返回下一个实例。

## 5. 触摸策略

每个密钥槽都有一个 User Interaction Flag（UIF）数据对象——`D6`（SIG）、`D7`（DEC）、`D8`（AUT）——保存两个字节：策略和确认类型（`20`，按键）。策略取值如下：

| 取值 | 含义 |
|:-----:|:--------|
| `00` | 关闭：不要求触摸 |
| `01` | 开启：要求触摸，在触摸缓存时间内缓存 |
| `02` | 永久开启：要求触摸，无法改回 |

设置策略需要验证 Admin PIN。取值为 `02` 的策略此后无法修改（会被拒绝并返回 `6985`）。触摸缓存时间存储在一字节数据对象 `0102` 中（0-255 秒；`00` 为出厂默认值，表示禁用缓存）。

触摸仅在 USB 接口上强制执行；NFC 连接时不要求触摸。如果触摸超时或被取消，命令失败并返回 `6600`。

相同的设置也可以通过 Admin 应用指令 `09h`（SIG / DEC / AUT 触摸策略和缓存时间）管理；参见 [Admin 应用文档](../admin/)。

## 6. Terminate 与 Activate

Terminate DF（`00 E6 00 00`）在 PW3 未被锁定时需要验证 Admin PIN；一旦 PW3 被锁定，则无需验证即可执行。终止之后，除 Select 和 Activate File 外的所有命令都会失败并返回 `6285`。

Activate File（`00 44 00 00`）仅在终止状态下被接受，并将应用恢复为出厂状态：所有密钥、证书、数据对象和 PIN 配置都会被重置为前文所述的默认值。

## 7. 状态码

| SW | 含义 |
|:---|:--------|
| `9000` | 成功 |
| `6285` | 应用已终止 |
| `63Cx` | 验证失败，剩余 x 次重试 |
| `6600` | 触摸超时或被取消 |
| `6700` | 长度错误 |
| `6982` | 安全状态不满足（需要验证） |
| `6983` | 密码已锁定 |
| `6985` | 条件不满足 |
| `6A80` | 数据错误 |
| `6A82` | 文件或应用未找到 |
| `6A86` | P1 / P2 错误 |
| `6A88` | 引用的数据未找到 |
| `6D00` | 指令不受支持 |
| `6E00` | 类别不受支持 |
