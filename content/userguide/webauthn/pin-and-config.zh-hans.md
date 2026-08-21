+++
title = "PIN 与配置"
date = 2022-06-08T20:30:05+08:00
weight = 1
+++

本页介绍 WebAuthn 应用的 PIN 行为以及固件版本 3.1.1 新增的配置选项。

## PIN

CanoKey 默认不设置 PIN，部分网站及部分功能（如 Discoverable Credentials 管理）要求您必须设置 PIN，请在收到提示时设置。

固件版本 2.0.0 及更高版本支持 PIN Protocol 2。

## 配置选项

固件版本 3.1.1 为 WebAuthn 应用新增了配置功能，包括：

- `alwaysUv`
- 最小 PIN 长度（`minPinLength`）
- 强制更改 PIN
- 长按重置设置

这些选项通过管理软件（如 CanoKey Console）进行配置。

{{% notice note %}}
使用固件 3.1.1 及更高版本时，需要关闭 `alwaysUv` 才能使用 U2F。
{{% /notice %}}
