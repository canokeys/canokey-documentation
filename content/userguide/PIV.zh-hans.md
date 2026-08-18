+++
title = "PIV"
date =  2020-07-11T22:33:15+08:00
weight = 25
+++

PIV（Personal Identity Verification，即个人身份认证）由美国联邦政府 [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf) 标准定义。PIV 可以储存用于签名和加密的密钥和证书，实现数字签名、文件加密等功能。

## 1. 基本信息

### 1.1 支持算法

* RSA2048
* NIST P-256
* NIST P-384

固件 3.0.0 及更高版本还支持以下扩展算法：

| 算法名称 | 算法ID |
|:-------|:-------|
| RSA3072| 05     |
| RSA4096| 16     |
| secp256k1|53    |
| Ed25519| E0     |
| X25519 | E1     |
| SM2    | 54     |

固件版本 3.1.1 起新增 NIST P-521（`secp521r1`，算法 ID 为 `15`）。管理软件可以读取设备当前使用的扩展算法 ID，具体数值以设备返回为准。

{{% notice note %}}
CanoKey 固件版本 3.0.0 仅支持使用 Ed25519 算法对 32 字节数据做签名，仅支持使用内部生成的 X25519 密钥。固件 3.0.2 及更高版本不受这些限制。
{{% /notice %}}

### 1.2 默认值

* PIN：123456
* PUK：12345678
* 管理密钥：`010203040506070801020304050607080102030405060708`

固件 3.1.1 及更高版本的 24 字节管理密钥使用 AES-192（算法 ID `0A`）。

### 1.3 密钥槽

CanoKey 支持如下密钥：

* 9A：PIV Authentication
* 9E：Card Authentication
* 9C：Digital Signature
* 9D：Key Management

从固件版本 2.0.0 开始，CanoKey 还支持 82、83 两个 Retired Key Management 槽位。

固件 3.1.1 及更高版本支持所有 Retired Key Management 槽位（82 至 95）。这些槽位及其证书仅在使用时创建，并占用设备的可用存储空间。

### 1.4 PIN 和触摸策略

#### PIN 策略
* 从不：从不验证 PIN
* 总是：每次使用都验证 PIN
* 一次：每个会话需要验证一次 PIN

#### 触摸策略
* 从不：从不需要触摸
* 总是：每次使用都需要触摸
* 缓存：如果在过去 15 秒内已触摸过，则不需要触摸，否则需要触摸

#### 默认策略

{{% notice note %}}
从固件版本 2.0.0 起，CanoKey 支持配置 PIV 的 PIN 和触摸策略。
{{% /notice %}}

| 密钥槽 | 默认 PIN 策略 | 默认触摸策略 |
|:------|:------------|:------------| 
| 9E    | 从不         | 从不       |
| 9C    | 总是         | 从不       |
| 9A、9D、82-95 | 一次 | 从不       |

上表中 9C 的默认值适用于固件 3.1.1 及更高版本。旧版固件的默认值可能不同，请通过 PIV 元数据查看设备实际采用的策略。

### 1.5 数据尺寸限制

* 证书：
  * 固件版本 1.5 或更早：1000字节
  * 固件版本 1.6 或更新：3000字节
* Card Capability Container：287字节
* Card Holder Unique Identifier：2916字节
* Printed Information：245字节
* Security Object：245 字节
* Cardholder Fingerprints、Facial Image 和 Iris Images：各 512 字节
* Key History：32 字节
* Admin Data：128 字节

固件 3.1.1 及更高版本支持所有 Retired Key Management 证书对象。读取 Printed Information、指纹、面部图像和虹膜图像时需要验证 PIN；写入这些对象时需要完成管理密钥认证。可选对象仅在写入数据后占用存储空间。

### 1.6 其他功能

从固件版本 2.0.0 起，CanoKey 支持查看 PIV 的元数据。

固件 3.1.1 及更高版本支持：

* 将 PIN 和 PUK 的重试次数设置为 1 至 15。该操作需要同时完成管理密钥认证和 PIN 认证，并会把 PIN、PUK 恢复为默认值。
* 为设备内部生成的密钥提供 PIV 设备证明（attestation，`INS F9`）。F9 槽位中须预置 P-256 设备证明密钥，其证书须写入数据对象 `5FFF01`；重置 PIV 应用不会删除这两项数据。导入的密钥不支持设备证明。
* 完成管理密钥认证后，在 PIV 槽之间移动密钥或删除密钥（`INS F6`）。对应证书不会随密钥一起移动或删除。
* 读写上述受 PIN 保护的 PIV 数据对象。

## 2. 常用操作

{{% notice note %}}
由于 PIV 通常为系统管理员签发，普通用户使用，因此请查阅文档理解下方内容后再操作。
{{% /notice %}}

### 2.1 工具

建议使用 [yubico-piv-tool](https://developers.yubico.com/yubico-piv-tool/Releases/) 执行相关操作。

### 2.2 分别导入密钥和证书

如果密钥和证书在两个文件，则需要分别导入。

导入私钥：
```sh
yubico-piv-tool -r canokey -a import-key -s 9a -i private-key.pem
```

导入证书：
```sh
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

其中，`-s 9a`表示使用9A密钥槽，可以根据需要进行更改。

### 2.3 导入PKCS#12文件

若要导入同时包含私钥和证书的 PKCS#12 文件（.p12 或 .pfx），执行：
```sh
yubico-piv-tool -r canokey -a import-key -a import-certificate -K PKCS12 -s 9a -i certificate.p12
```

### 2.4 生成密钥并自签名

生成一个新的私钥并对其自签名：
```sh
yubico-piv-tool -r canokey -a generate -s 9a -A RSA2048 -o public-key.pem
yubico-piv-tool -r canokey -a verify-pin -a selfsign -s 9a -S "/CN=Test Certificate" -i public-key.pem -o certificate.pem
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

### 2.5 Windows 的额外操作

由于 Windows 会根据 CHUID 来缓存卡内证书信息，因此在 Windows 上导入证书后，需要更新 CHUID：
```sh
yubico-piv-tool -r canokey -a set-chuid
```
