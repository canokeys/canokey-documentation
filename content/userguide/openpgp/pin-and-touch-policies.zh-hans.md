+++
title = "PIN 和触摸策略"
date = 2020-07-04T16:19:06+08:00
weight = 1
+++

CanoKey 的 OpenPGP 应用使用用户 PIN、Admin PIN 和可选的 Reset Code，并且可以在执行密码学操作前要求物理触摸。本页介绍各项默认值及策略语义。

## 默认值

| 项目 | 默认值 | 最小长度 | 最大长度 |
| --- | --- | --- | --- |
| PIN | `123456` | 6 | 64 |
| Admin PIN | `12345678` | 8 | 64 |
| Reset Code | 空 | 8 | 64 |
| Signature PIN | forced（每次签名都要验证 PIN） | — | — |
| 触摸策略（SIG, DEC, AUT） | 关闭 | — | — |
| 触摸缓存时间 | 0 | — | — |
| 重试次数（PIN、Reset Code、Admin PIN） | 3 | — | — |

固件版本 3.1.1 起允许管理软件将 PIN、Reset Code 和 Admin PIN 的重试次数设置为 1 至 15。修改后，PIN 和 Admin PIN 将恢复为默认值；已有的 Reset Code 不会改变，但其重试计数会重置。

## PIN 策略

对于 DEC 和 AUT 密钥，PIN 验证成功后，将不再需要验证，直到断开并重新插入 CanoKey。

对于 SIG，如果 `forcesig` 打开，则每次签名都要求输入 PIN；否则，只在上电后的第一次签名时要求输入 PIN。

## 触摸策略

{{% notice note %}}
触摸策略仅在使用 USB 接口时生效。
{{% /notice %}}

根据固件版本不同，你可以在 CanoKey Console 中或通过 `gpg` 命令设置 SIG、DEC 和 AUT 的触摸策略。触摸缓存时间的值在 0 至 255 秒之间（0 为无缓存）。

### 固件版本 < 1.4

请通过 CanoKey Console 的“设置”应用修改触摸策略。

### 固件版本 >= 1.5.0

请通过 GnuPG 修改触摸策略。
