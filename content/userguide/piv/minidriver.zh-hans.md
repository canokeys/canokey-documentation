+++
title = "Windows Minidriver"
date = 2026-09-04T00:00:00+08:00
weight = 11
+++

[CanoKey Windows minidriver](https://github.com/canokeys/canokey-mini-driver) 让
Windows 应用通过 Microsoft Smart Card CSP 和 Smart Card KSP 使用 PIV 证书和
私钥。这一层负责 Windows 集成；完整的 PIV 槽位和算法仍通过
[PKCS#11](pkcs11/) 提供。

## Windows 支持的功能

Minidriver 提供六个稳定的 Windows 容器：

| 容器索引 | PIV 槽位 | Windows 用途 |
|:--------:|:--------:|:-------------|
| 0 | `9A` | 认证和签名 |
| 1 | `9C` | 签名 |
| 2 | `9D` | 签名；RSA 密钥交换/解密 |
| 3 | `9E` | 认证和签名 |
| 4 | `82` | 签名和退休密钥管理 |
| 5 | `83` | 签名和退休密钥管理 |

Windows 在这些容器中支持 RSA 以及 NIST P-256、P-384、P-521。Ed25519、
X25519、secp256k1、SM2、ML-DSA、ML-KEM 和更高编号的退休槽位只能通过
PKCS#11 使用，因为当前 Windows 智能卡接口没有安全的表示方式。EC 曲线根据
完整的曲线参数识别，不能仅凭 32 字节坐标判定为 P-256。

## 开发环境安装

开发流程不会安装 INF。构建 DLL、复制到 debug 目录，然后在 Calais 注册表中
将 CanoKey ATR 映射到它。默认路径是 `C:\canokey-minidriver\canokey-minidriver.dll`，
cardmod 使用的值名是 `80000001`：

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\Calais\SmartCards\CanoKey]
"ATR"=hex:3b,f7,11,00,00,81,31,fe,65,43,61,6e,6f,6b,65,79,99
"ATRMask"=hex:ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff
"Crypto Provider"="Microsoft Base Smart Card Crypto Provider"
"Smart Card Key Storage Provider"="Microsoft Smart Card Key Storage Provider"
"80000001"="C:\\canokey-minidriver\\canokey-minidriver.dll"
```

这是本地调试用的注册表映射，不是生产驱动安装。替换 DLL 后，重新插拔
CanoKey，或重启已经加载旧模块的应用。

## 运行时配置

可选配置位于 `HKLM\SOFTWARE\Canokeys\ckmd`：

| 值 | 类型和值 | 默认值/作用 |
|:---|:---------|:------------|
| `LogPath` | 字符串 | 缺省时关闭日志；设置日志文件路径后启用 |
| `LogLevel` | `trace`、`debug`、`info`、`warn`、`error`、`fatal`、`none` | 启用日志时默认为 `warn` |
| `LogSensitiveData` | DWORD 或布尔文本 | `0`；启用原始 APDU/hex 输出 |
| `ProtectManagement` | DWORD 或布尔文本 | `1`；用户 PIN 登录后尝试 PIN-managed 管理密钥恢复 |
| `RefreshDeviceKeys` | DWORD 或布尔文本 | `1`；刷新外部密钥/证书变化 |
| `RefreshWindow` | 秒数 | `60`；`0` 表示每次实时元数据读取都刷新 |
| `NewKeyTouchPolicy` | `1` never、`2` always、`3` cached | Windows 新建密钥默认为 `1` |
| `NewKeyPinPolicy` | `1` never、`2` once、`3` always | 缺省使用 PIV 默认值；`3` 当前拒绝 |
| `PinCacheTimeout` | 秒数 | 报告给 Windows 的 PIN 缓存建议时长 |

除非明确需要调试 APDU，否则应保持 `LogSensitiveData=0`；日志可能包含认证或
私钥操作数据。

## PIN-managed 操作

PIN-protected 管理密钥模式及其永久阻断 PUK 的取舍，参见[PIN、PUK 与管理密钥](pin-puk-management-key/)。
卡片完成配置后，minidriver 会在用户认证后自动尝试使用该模式。

保持 `ProtectManagement=1`（默认值）即可启用自动检查。当外部配置系统负责管理
密钥、不希望执行额外检查时，可设为 `0`。该设置不会配置卡片，也不会阻断 PUK。
如果受保护数据缺失、格式错误或 PUK 未锁定，minidriver 只保留普通用户认证，
管理操作需要其他受支持的管理员路径。

## 证书、缓存和限制

Minidriver 提供证书文件供 Windows 枚举和签名；证书写入以及密钥生成/导入仍需管理
授权。`cardid`、`cardcf` 和 `mscp/cmapfile` 用于 Windows 缓存协调，但实时 PIV 元数据
才是权威状态。由于 PIV 没有持久化 PIN freshness counter，报告的缓存模式为 no-cache。

使用 `certutil -scinfo` 检查 Windows 能否枚举读卡器和证书。minidriver 仓库还提供
通过 CAPI/CNG 执行签名、RSA 解密和 ECDH 原始共享密钥派生的 PowerShell 测试。
KSP 中看不到某项能力不代表卡片没有该能力；请查看 [PKCS#11 接口](pkcs11/)。
