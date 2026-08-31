+++
title = "PIN、PUK 与管理密钥"
date = 2020-07-11T22:33:15+08:00
weight = 2
+++

PIV 应用由三个密钥保护：PIN、PUK 和管理密钥。它们分别保护不同类别的操作。

## PIN

PIN 是日常使用的用户凭据。根据各槽位的 [PIN 策略](pin-touch-policies/)，使用槽内私钥进行签名、解密或密钥协商前可能需要先验证 PIN。默认 PIN 为 `123456`。

## PUK

PUK（PIN Unblocking Key，PIN 解锁密钥）用于恢复被锁定的 PIN。当 PIN 连续输错次数过多而被锁定后，可通过 Reset Retry Counter 指令使用 PUK 设置新的 PIN 并恢复其重试次数。默认 PUK 为 `12345678`。

## 管理密钥

管理密钥是管理员凭据，为 24 字节 Triple-DES 密钥，默认值为 `010203040506070801020304050607080102030405060708`。管理密钥本身可通过 Set Management Key 指令更换。

## 锁定与解锁

连续输错 PIN 会耗尽其重试次数，此后 PIN 被锁定，需要 PIN 的操作将失败。被锁定的 PIN 可以使用 PUK 解锁。如果 PUK 也被锁定，则可以重置 PIV 应用——Reset 指令仅在 PIN 和 PUK 均被锁定时才被接受。重置会将 PIV 用户数据恢复为默认值，并清空可写数据对象。

## 各类操作所需的密钥

需要管理密钥认证的操作：

* 在槽位中生成密钥对
* 导入非对称密钥
* 写入数据对象（包括证书）
* 更换管理密钥

需要 PIN 的操作：

* 按槽位的 [PIN 策略](pin-touch-policies/)使用私钥进行签名、解密或密钥协商
* 修改 PIN

需要 PUK 的操作：

* 解锁被锁定的 PIN（Reset Retry Counter）
