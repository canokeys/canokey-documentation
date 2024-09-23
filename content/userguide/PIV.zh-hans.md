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

从 CanoKey Canary 起，还支持以下扩展算法：

| 算法名称 | 算法ID |
|:-------|:-------|
| RSA3072| 05     |
| RSA4096| 16     |
| secp256k1|53    |
| Ed25519| E0     |
| X25519 | E1     |
| SM2    | 54     |

### 1.2 默认值

* PIN：123456
* PUK：12345678
* 管理密钥：`010203040506070801020304050607080102030405060708`

### 1.3 密钥槽

CanoKey 支持如下密钥：

* 9A：PIV Authentication
* 9E：Card Authentication
* 9C：Digital Signature
* 9D：Key Management

从固件版本 2.0.0 开始，CanoKey 还支持如下密钥：

* 82、83

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
| 9E    | 不验证       | 从不       |
| 其他   | 一次        | 从不       |

### 1.5 数据尺寸限制

* 证书：
  * 固件版本 1.5 或更早：1000字节
  * 固件版本 1.6 或更新：3000字节
* Card Capability Container：287字节
* Card Holder Unique Identifier：2916字节
* Printed Information：245字节

### 1.6 其他功能

从固件版本 2.0.0 起，CanoKey 支持查看 PIV 的元数据。

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
