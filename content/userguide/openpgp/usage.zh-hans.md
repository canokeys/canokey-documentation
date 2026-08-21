+++
title = "常用操作"
date = 2020-07-04T16:19:06+08:00
weight = 2
+++

有关 OpenPGP 和 GnuPG 的一般用法，请参阅 [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html)。本页概括了使用 `gpg` 进行的常见卡片操作。

## 查看卡片

连接 CanoKey 后，运行：

```bash
gpg --card-status
```

该命令会显示卡片信息，例如序列号、SIG / DEC / AUT 槽位中存储的密钥，以及当前的重试计数。

## 管理卡片

进入交互式卡片管理模式：

```bash
gpg --card-edit
```

在卡片管理提示符下：

- 输入 `admin` 以启用管理命令（其中大多数需要提供 Admin PIN）。
- 输入 `generate` 在卡片上生成新的密钥对。固件版本 2.0.0 起支持在卡片上生成 RSA3072 / RSA4096 密钥；在更早的固件上，请在计算机上生成这些密钥后再导入。
- 输入 `passwd` 修改 PIN、Admin PIN 或 Reset Code。

## 在卡片上生成密钥

1. 运行 `gpg --card-edit`。
2. 输入 `admin`，然后输入 `generate`。
3. 按提示选择密钥类型，并可选择为加密密钥创建卡外备份。

密钥在 CanoKey 内部生成，不会以明文形式离开卡片。

## 导入已有密钥

如果你已有 GnuPG 密钥对，可以使用 `keytocard` 将其子钥转移到卡片上：

1. 在计算机上私钥可用的情况下，运行 `gpg --edit-key <KEYID>`。
2. 使用 `key <N>`（例如 `key 1`）选中一个子钥，然后输入 `keytocard` 并选择目标槽位（签名、加密或验证）。
3. 输入 `save` 保存。

{{% notice warning %}}
`keytocard` 会将私钥转移到卡片上；本地副本将被替换为指向卡片的占位（stub）。如需保留卡外副本，请事先备份私钥。
{{% /notice %}}

## 卡片被锁定

如果重试次数耗尽且忘记了 Admin PIN，可以在 CanoKey Console 中重置 OpenPGP 应用。这会恢复 [PIN 和触摸策略](pin-and-touch-policies/) 中列出的出厂默认值，并删除 OpenPGP 应用中存储的所有密钥。
