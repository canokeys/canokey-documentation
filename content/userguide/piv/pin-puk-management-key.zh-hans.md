+++
title = "PIN、PUK 与管理密钥"
date = 2020-07-11T22:33:15+08:00
weight = 2
+++

PIV 应用由三个密钥保护：PIN、PUK 和管理密钥。它们分别保护不同类别的操作。

## PIN

PIN 是日常使用的用户凭据。根据各槽位的 [PIN 策略](pin-touch-policies/)，使用槽内私钥进行签名、解密或密钥协商前可能需要先验证 PIN。PIN 还保护部分数据对象的读取权限，例如 Printed Information 和生物特征对象。默认 PIN 为 `123456`。

## PUK

PUK（PIN Unblocking Key，PIN 解锁密钥）用于恢复被锁定的 PIN。当 PIN 连续输错次数过多而被锁定后，可通过 Reset Retry Counter 指令使用 PUK 设置新的 PIN 并恢复其重试次数。默认 PUK 为 `12345678`。

## 管理密钥

管理密钥是管理员凭据，为 24 字节对称密钥，默认值为 `010203040506070801020304050607080102030405060708`。固件 3.1.1 及更高版本使用 AES-192（算法 ID `0A`）；更早的固件使用 Triple-DES。管理密钥本身可通过 Set Management Key 指令更换。

## PIN-protected 管理密钥（PIN-only）

CanoKey 支持 Yubico 兼容的 PIN-protected 管理密钥模式。用户 PIN 验证成功后，PKCS#11 模块可以从受保护的 PIV 数据中恢复并认证管理密钥，使 Windows 证书注册等流程能够在普通 PIN 提示后使用管理权限。当前不支持 PIN-derived 模式。

PIN-protected 模式是一次性的卡片配置决定，不是修改注册表就能切换的开关。卡片必须已经写入受保护的管理密钥数据和匹配的 ADMIN DATA，并且 PUK 必须实际锁定（重试次数为 0）。阻断 PUK 可以防止 PUK 持有者重置 PIN 后恢复管理权限，但会永久失去 PUK 恢复路径。

对于已经准备好的开发卡，PKCS#11 项目提供 `finalize-pin-managed.ps1`。该脚本要求显式确认、期望的 slot ID 和 token serial，才会阻断 PUK。它不是通用配置向导；没有明确的生产恢复策略时不要运行。

配置完成后，Windows minidriver 会在用户认证后自动使用该模式。`ProtectManagement` 设置和 Windows 特有行为参见 [Windows Minidriver](minidriver/)。

## 锁定与解锁

连续输错 PIN 会耗尽其重试次数，此后 PIN 被锁定，需要 PIN 的操作将失败。被锁定的 PIN 可以使用 PUK 解锁。如果 PUK 也被锁定，则可以重置 PIV 应用——Reset 指令仅在 PIN 和 PUK 均被锁定时才被接受。重置会将 PIV 用户数据恢复为默认值，但保留 F9 槽位的设备证明密钥和数据对象 `5FFF01` 中的设备证明证书；其余可写数据对象将被清空。

## 修改重试次数

固件 3.1.1 及更高版本支持将 PIN 和 PUK 的重试次数设置为 1 至 15。该操作需要同时完成管理密钥认证和 PIN 验证，并会把 PIN、PUK 恢复为默认值。

## 各类操作所需的密钥

需要管理密钥认证的操作：

* 在槽位中生成密钥对
* 导入非对称密钥
* 写入数据对象（包括证书）
* 更换管理密钥
* 在槽位之间移动密钥或删除密钥（固件 3.1.1 及更高版本）
* 设置 PIN 和 PUK 重试次数（固件 3.1.1 及更高版本，另需验证 PIN）

需要 PIN 的操作：

* 按槽位的 [PIN 策略](pin-touch-policies/)使用私钥进行签名、解密或密钥协商
* 读取受 PIN 保护的数据对象（Printed Information、Cardholder Fingerprints、Cardholder Facial Image、Cardholder Iris Images）
* 修改 PIN

需要 PUK 的操作：

* 解锁被锁定的 PIN（Reset Retry Counter）
