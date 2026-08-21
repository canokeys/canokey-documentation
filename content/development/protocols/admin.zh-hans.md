---
title: "Admin 应用"
date: 2019-11-28T10:18:29-05:00
weight: 30
---

要管理您的 CanoKey，可以使用 Admin 应用来：

- 重置 OpenPGP / PIV / OATH。
- 导入 FIDO 私钥和证书。

### 1. 基本定义

#### AID

Admin 应用的 AID 为 `F000000000`。

#### 指令（Instructions）

标记为“需要 PIN”的指令，需要先成功执行 Verify PIN 命令后才可使用。

| 名称                 | 编码 | 需要 PIN |
| -------------------- | ---- | -------- |
| Write FIDO Key       | 01h  | Y        |
| Write FIDO Cert      | 02h  | Y        |
| Reset OpenPGP        | 03h  | Y        |
| Reset PIV            | 04h  | Y        |
| Reset OATH           | 05h  | Y        |
| Export OATH          | 06h  | Y        |
| Reset NDEF           | 07h  | Y        |
| Set NDEF Read-only   | 08h  | Y        |
| OpenPGP Touch Policy | 09h  | Y        |
| Verify PIN           | 20h  | N        |
| Change PIN           | 21h  | Y        |
| Write SN             | 30h  | Y        |
| Get Version          | 31h  | N        |
| Get SN               | 32h  | N        |
| Config               | 40h  | Y        |
| Flash Usage          | 41h  | Y        |
| Read Config          | 42h  | Y        |
| Factory Reset        | 50h  | N        |
| Select               | A4h  | N        |
| Vendor Specific      | FFh  | Y        |

### 2. Select

选择应用以供使用。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | A4h   |
| P1    | 04h   |
| P2    | 00h   |
| Lc    | AID 的长度（5） |
| Data  | AID（F0 00 00 00 00） |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 3. Verify PIN

验证本 Admin 应用的 PIN。默认 PIN 为 `123456`（字符串形式）或 `31 32 33 34 35 36`（十六进制形式）。

{{% notice note %}}
Admin / OpenPGP / PIV 各应用的 PIN 相互独立。
{{% /notice %}}

{{% notice warning %}}
最大重试次数为 3。超过此限制后，应用将被锁定。验证成功会重置该计数。
{{% /notice %}}

如果输入为空（Lc = 0），则返回 PIN 的实际验证状态。如果 PIN 已验证，应用返回正常状态字（SW = 9000）。如果 PIN 尚未验证而又需要验证，应用返回状态字 63CX，其中 'X' 表示剩余的可重试次数。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 20h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | PIN 的长度或 0 |
| Data  | PIN |

#### 响应

| SW   | 描述 |
| ---- | ---- |
| 9000 | 成功 |
| 63CX | 验证失败，剩余 X 次重试机会 |
| 6983 | 应用已被锁定 |

### 4. Change PIN

验证成功后，您可以使用此命令**直接**修改 PIN。PIN 长度应在 6 到 64 之间。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 21h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 新 PIN 的长度 |
| Data  | 新 PIN |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |
| 6700 | 长度错误 |

### 5. Write FIDO Key

您可以使用此命令手动写入私钥。私钥应为 secp256r1 (NIST P-256) 密钥。

{{% notice note %}}
私钥更新后，证书也应相应更新。
{{% /notice %}}

{{% notice warning %}}
一旦写入新的私钥，您原有的 2FA 凭据（FIDO2）将**失效**。
{{% /notice %}}

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 01h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 密钥长度（20h） |
| Data  | 私钥 |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |
| 6700 | 长度错误 |

### 6. Write FIDO Certification

FIDO 证书是与您的私钥对应的 X.509 der 格式证书。请使用**扩展命令 APDU**（extended command APDU）进行设置。

{{% notice note %}}
证书的最大长度为 1152 字节。
{{% /notice %}}

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 02h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 证书长度（2 字节） |
| Data  | 证书 |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |
| 6700 | 长度错误 |

### 7. Reset OpenPGP / PIV / OATH / NDEF

执行这些命令将重置相应的应用。

| 指令编码         | 应用    |
| ---------------- | ------- |
| 03h              | OpenPGP |
| 04h              | PIV     |
| 05h              | OATH    |
| 07h              | NDEF    |

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 03h / 04h / 05h / 07h |
| P1    | 00h   |
| P2    | 00h   |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 8. Export OATH

导出 OATH 应用中的数据。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 06h   |
| P1    | 00h   |
| P2    | 00h   |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 9. Change NDEF read-only

设置 NDEF 是否为只读。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 08h   |
| P1    | 00h 表示可读写，01h 表示只读 |
| P2    | 00h   |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 10. OpenPGP Touch Policy

设置 OpenPGP 操作是否需要触摸。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 09h   |
| P1    | 00h 表示 SIG，01h 表示 DEC，02h 表示 AUT，03h 表示缓存时间 |
| P2    | 当 P1 为 00/01/02 时，00h 表示不需要触摸，01h 表示需要触摸。当 P1 为 03h 时，为缓存时间（单位：秒） |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 11. Write SN

SN 只能写入**一次**。受 OpenPGP 卡规范限制，序列号长度为 4 字节。

{{% notice note %}}
如果您自行制作 CanoKey，应使用此命令写入 SN。否则，SN 已经写入完毕。
{{% /notice %}}

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 30h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | SN 的长度（4） |
| Data  | SN    |

#### 响应

| SW   | 描述       |
| ---- | ---------- |
| 9000 | 成功       |
| 6700 | 长度错误   |
| 6985 | SN 已写入  |

### 12. Get version

读取固件版本和硬件版本。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 31h   |
| P1    | 00h 表示固件版本，01h 表示硬件版本 |
| P2    | 00h   |
| LE    | 00h   |

#### 响应

以 UTF-8 编码的版本号。

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 13. Get serial number

读取 CanoKey 的序列号和芯片 ID。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 32h   |
| P1    | 00h 表示 CanoKey SN，01h 表示芯片 ID |
| P2    | 00h   |
| LE    | 00h   |

#### 响应

原始数据。

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 14. Config

配置 USB 接口和 LED 状态：

- LED 可配置为在不闪烁时常亮（ON）或熄灭（OFF）。**默认值为 ON。**
- 键盘接口启用后，只需触摸密钥即可输入 HOTP。**默认值为 OFF。**

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 40h   |
| P1    | 01h：LED；03h：键盘 |
| P2    | 00h：关闭，01h：开启 |

#### 响应

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 15. Get flash usage

获取闪存容量。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 41h   |
| P1    | 00h   |
| P2    | 00h   |

#### 响应

共 2 字节。第一个字节表示剩余空间（单位 KiB），第二个字节表示闪存总容量（单位 KiB）。

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 16. Get current configurations

获取当前配置。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 42h   |
| P1    | 00h   |
| P2    | 00h   |

#### 响应

共 7 字节。

| 字节 | 含义           |
| ---- | -------------- |
| 1    | LED            |
| 2    | 键盘           |
| 3    | NDEF 只读      |
| 4    | OpenPGP SIG 触摸策略 |
| 5    | OpenPGP DEC 触摸策略 |
| 6    | OpenPGP AUT 触摸策略 |
| 7    | OpenPGP 触摸缓存时间 |

| SW   | 描述     |
| ---- | -------- |
| 9000 | 成功     |

### 17. Factory Reset

重置各应用（FIDO 密钥/证书和 SN 不会被重置）。
必须先用完 PIN 重试次数才能开始重置。
命令执行后，您必须在 **LED 闪烁时 2 秒内触摸**，直到其返回 `9000`。

#### 请求

| 字段 | 值 |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 50h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 05h   |
| Data  | RESET（ASCII 编码） |

#### 响应

| SW   | 描述                 |
| ---- | -------------------- |
| 9000 | 成功                 |
| 6982 | 闪烁时未触摸         |
| 6985 | PIN 尚未锁定         |

### 18. Vendor specific

此命令用于 NFC 配置，不应直接使用。
