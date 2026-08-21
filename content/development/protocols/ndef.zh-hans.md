---
title: "NDEF 应用"
date: 2026-08-21T00:00:00+08:00
weight: 15
---

CanoKey 的 NDEF 应用实现了 [NFC Forum Type-4 Tag](http://apps4android.org/nfc-specifications/NFCForum-TS-Type-4-Tag_2.0.pdf)，通过 NFC 接口暴露一条 NDEF 消息。本页介绍 CanoKey 所实现的文件布局和命令。标准定义请参阅 NFC Forum 规范。

## 1. AID 与文件选择

NDEF 标签应用标识符（AID）为：

```text
D2 76 00 00 85 01 01
```

使用 `00 A4 04 00 07 D2760000850101` 选择该应用。随后，使用 Select 命令（`P1 = 00`，`P2 = 0C`）并在数据域中给出两字节的文件标识符，选择以下两个文件之一：

| 文件 ID | 文件 |
|:-------:|:-----|
| `E103` | Capability Container（CC） |
| `0001` | NDEF 文件 |

### 1.1 指令

| INS | 名称 |
|:---:|:-----|
| `A4` | Select |
| `B0` | Read Binary |
| `D6` | Update Binary |

固件 3.1.1 及更高版本支持 Update Binary 的 ISO 7816-4 命令链（`CLA = 10`）。

## 2. Capability Container

Capability Container 长度为 15 字节，从标签接口只能读取：

| 偏移 | 长度 | 字段 | 值 |
|:------:|:------:|:------|:------|
| 0 | 2 | CCLEN | `000F` |
| 2 | 1 | 映射版本 | `20`（版本 2.0） |
| 3 | 2 | MLe | `0400` |
| 5 | 2 | MLc | `0400` |
| 7 | 1 | NDEF File Control TLV：标签 | `04` |
| 8 | 1 | NDEF File Control TLV：长度 | `06` |
| 9 | 2 | NDEF 文件标识符 | `0001` |
| 11 | 2 | NDEF 文件最大大小 | `0400` |
| 13 | 1 | NDEF 文件读权限 | `00`（允许读取） |
| 14 | 1 | NDEF 文件写权限 | `00`（允许写入）或 `FF`（只读） |

对 Capability Container 执行 Update Binary 会被拒绝并返回 `6985`；写权限字节只能通过 Admin 应用修改（见第 5 节）。

## 3. NDEF 文件

NDEF 文件最多可容纳 1024 字节：开头是两字节大端序的 NLEN，随后是 NDEF 消息，因此 NDEF 消息的最大长度为 1022 字节。

出厂默认内容是一条指向 `https://canokeys.org` 的 URI 记录：

```text
00 11                                        ; NLEN = 17
D1 01 0D 55 04 63 61 6E 6F 6B 65 79 73 2E 6F 72 67
```

（`D1`：短记录，类型名格式 1；类型 `55` = "U"，即 URI；标识符 `04` = `https://` 前缀；负载为 `canokeys.org`。）

## 4. Read Binary 与 Update Binary

Read Binary（`INS = B0`）在 `P1:P2` 中携带偏移量（大端序），在 `Le` 中给出要读取的字节数。超出所选文件末尾的读取会被拒绝并返回 `6700`。在未选择文件的情况下发出该命令会被拒绝并返回 `6985`。

Update Binary（`INS = D6`）在 `P1:P2` 中携带偏移量，在命令数据域中携带数据；`offset + Lc` 不得超过 NDEF 文件大小（1024 字节）。仅当 CC 的写权限字节为 `00` 时才接受写入；否则命令失败并返回 `6982`。

要替换 NDEF 消息，应先更新消息字节，然后再更新偏移 0 处的 NLEN 字段，这是 Type-4 Tag 的常规做法。

## 5. 只读标志

可通过 Admin 应用的指令 `08h` 将 NDEF 消息设为只读（`P1 = 00` 表示读写，`P1 = 01` 表示只读），该指令会重写 Capability Container 的写权限字节；参见 [Admin 应用文档](../admin/)。设为只读后，对 NDEF 文件执行 Update Binary 会失败并返回 `6982`。

Admin 应用还可以重置 NDEF 应用（指令 `07h`），这会恢复默认的 Capability Container 和默认的 `https://canokeys.org` 内容。

## 6. 状态码

| SW | 含义 |
|:---|:--------|
| `9000` | 成功 |
| `6700` | 长度或偏移错误 |
| `6982` | 安全状态不满足（写权限已禁用） |
| `6985` | 条件不满足（未选择文件；CC 不可写） |
| `6A82` | 文件未找到（未知的文件标识符） |
| `6A86` | P1 / P2 错误 |
| `6D00` | 不支持的指令 |
