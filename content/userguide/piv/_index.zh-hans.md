+++
title = "PIV"
date = 2020-07-11T22:33:15+08:00
weight = 25
+++

PIV（Personal Identity Verification，即个人身份认证）由美国联邦政府 [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf) 标准定义。PIV 可以储存用于签名和加密的密钥和证书，实现数字签名、文件加密等功能。CanoKey 的 PIV 应用实现了 [NIST SP 800-73-4](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-73-4.pdf) 规定的必备功能，并提供了若干 Yubico 兼容及 CanoKey 特有的扩展；协议层面的细节参见[开发文档](/development/protocols/piv/)。

## 支持算法

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

固件 3.1.1 及更高版本还支持：

| 算法名称 | 算法ID |
|:-------|:-------|
| NIST P-521（`secp521r1`） | 15 |
| ML-DSA-65 | E2 |
| ML-KEM-768 | E3 |

扩展算法的 ID 是可配置的；管理软件可以读取设备当前使用的扩展算法 ID，具体数值以设备返回为准。

{{% notice note %}}
CanoKey 固件版本 3.0.0 仅支持使用 Ed25519 算法对 32 字节数据做签名，仅支持使用内部生成的 X25519 密钥。固件 3.0.2 及更高版本不受这些限制。
{{% /notice %}}

## 本章内容

* [密钥槽](slots/)——PIV 的各个槽位及其用途
* [PIN、PUK 与管理密钥](pin-puk-management-key/)——保护 PIV 应用的三个密钥
* [PIN 与触摸策略](pin-touch-policies/)——各槽位的验证与触摸要求
* [证书](certificates/)——证书对象及其容量
* [数据对象](data-objects/)——其他 PIV 数据对象及其尺寸限制
* [设备证明](attestation/)——证明密钥是在设备上生成的
* [密码学操作](crypto-operations/)——签名、解密与密钥协商
* [元数据与密钥管理](metadata/)——读取元数据、移动与删除密钥
* [常用操作](operations/)——使用 yubico-piv-tool 完成常见任务
* [PKCS#11 集成](pkcs11/)——面向应用的完整 PIV 接口
* [Windows Minidriver](minidriver/)——CSP/KSP 集成及其支持范围
