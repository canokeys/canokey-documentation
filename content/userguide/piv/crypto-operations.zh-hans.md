+++
title = "密码学操作"
date = 2020-07-11T22:33:15+08:00
weight = 7
+++

各个非对称密钥槽面向不同种类的密码学操作：

| 槽位 | 用途 |
|:-----|:-----|
| 9A（PIV Authentication） | 认证用户，通常用于系统登录 |
| 9C（Digital Signature） | 签名邮件、文件、可执行程序、Git 提交等 |
| 9D（Key Management） | 加密以保证机密性，例如解密邮件 |
| 9E（Card Authentication） | 认证卡片，通常用于门禁 |
| 82–83（Retired Key Management） | 解密用证书已过期的旧密钥加密的数据 |

签名并不限于槽位 9C：9A、9D、9E、82 和 83 槽位中的密钥同样可以用于签名。槽位 9B 存放对称密钥，不能签名。

槽位 9D 用于 RSA 密钥的解密和 EC 密钥的 ECDH 密钥协商。

按照 PIV 标准的要求，ECDSA 签名以 ASN.1 DER 编码返回。

## 与 PIN 和触摸策略的关系

操作是否需要输入 PIN、触摸或两者，取决于该槽位配置的 [PIN 与触摸策略](pin-touch-policies/)。例如按默认策略，槽位 9E 不需要验证 PIN，而槽位 9A 每个会话只需验证一次。触摸要求仅在 USB 连接时生效，NFC 连接时不强制执行。设备实际采用的策略可通过[元数据](metadata/)读取。
