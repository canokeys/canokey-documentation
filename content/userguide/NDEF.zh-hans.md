+++
title = "NDEF"
date =  2020-12-27T02:30:15+08:00
weight = 30
+++

## 默认值

* 模式：默认为读写模式，可在网络控制台中通过使用[网页小程序](https://console.canokeys.org/) 进行切换
* 内容：默认 NDEF 信息，URL 为 "https://canokeys.org"
* 最大 NDEF 报文长度：1022 字节

## 使用

您可以将其用作包含 URL、vCard 或任何 NDEF 支持的 NFC 标签。
**请注意，由于 NDEF 没有加密功能，因此秘密不应存储在 NDEF 中。**


您可以使用 [NFC 工具](https://play.google.com/store/apps/details?id=com.wakdev.wdnfc)来处理 NDEF 信息。
