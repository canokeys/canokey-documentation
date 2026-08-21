+++
title = "数据对象"
date = 2020-07-11T22:33:15+08:00
weight = 5
+++

除证书外，PIV 应用还存储若干标准数据对象。下表列出各对象的标签和容量。

| 标签 | 数据对象 | 容量 | 读取权限 |
|:----|:---------|-----:|:--------|
| `5FC102` | Card Holder Unique Identifier（CHUID） | 2916 字节 | 公开 |
| `5FC107` | Card Capability Container | 287 字节 | 公开 |
| `5FC109` | Printed Information | 245 字节 | PIN |
| `5FC106` | Security Object | 245 字节 | 公开 |
| `5FC103` | Cardholder Fingerprints | 512 字节 | PIN |
| `5FC108` | Cardholder Facial Image | 512 字节 | PIN |
| `5FC121` | Cardholder Iris Images | 512 字节 | PIN |
| `5FC10C` | Key History | 32 字节 | 公开 |
| `5FFF00` | Admin Data | 128 字节 | 公开 |

读取 Printed Information、Cardholder Fingerprints、Cardholder Facial Image 和 Cardholder Iris Images 时需要验证 PIN；写入这些对象时需要完成管理密钥认证。可选对象仅在写入数据后占用存储空间。

证书对象（包括固件 3.1.1 起支持的全部 Retired Key Management 证书对象）见[证书](certificates/)。
