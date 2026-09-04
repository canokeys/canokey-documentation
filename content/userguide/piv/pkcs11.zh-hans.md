+++
title = "PKCS#11 集成"
date = 2026-09-04T00:00:00+08:00
weight = 10
+++

[CanoKey PKCS#11 模块](https://github.com/canokeys/canokey-pkcs11) 让桌面应用
通过标准 PKCS#11 接口访问 PIV 应用。模块提供 PKCS#11 3.2 接口，同时保留
仍使用旧接口的应用所需的 2.40 函数表。

## 支持的功能

模块可以发现 PIV 槽位 `9A`、`9C`、`9D`、`9E` 以及退休槽位 `82` 至 `95`。
空槽位不会显示。卡片上存在的 PIV 数据对象（包括证书、CHUID 和元数据）也会
作为 PKCS#11 对象提供。

| 能力 | 支持情况 |
|:-----|:---------|
| RSA 2048/3072/4096 | 生成/导入、签名、RSA 解密 |
| NIST P-256/P-384/P-521 | 生成/导入、ECDSA 签名/验证、ECDH |
| Ed25519 | 生成/导入、纯 EdDSA 签名 |
| X25519 | 生成/导入、ECDH 派生 |
| ML-DSA-65 | 生成/导入、卡片签名、主机验证 |
| ML-KEM-768 | 生成/导入、主机封装、卡片解封装 |
| PIV 数据对象 | 存在时可读；写入需要管理权限 |
| 卡片随机数 | 固件 6.0 及更高版本支持 |

扩展算法 ID 会从卡片读取。对于修改过算法配置的卡，不应硬编码文档中的默认值。

## 不支持的功能

* SM2 密钥仍可被发现且曲线身份保持正确，但本模块不提供 SM2 签名或密钥协商。
* 使用 PIN-always 密钥进行 ECDH 或其他一次性派生操作会被拒绝，因为当前 PIV
  协议没有对应的逐操作 PIN 步骤。
* 不支持由主机提供 RNG seed；卡片会自行初始化随机源。
* 不支持 PKCS#11 message、异步操作和 authenticated-wrap 操作。
* 模块不会把 PIV 密钥转换成 OpenPGP 密钥。OpenPGP 应用应使用 OpenPGP 应用
  及其独立的集成路径。

RSA 公钥加密以及 RSA、ECDSA、ML-DSA 验证在主机库中执行。私钥签名、RSA 解密、
ECDH 和 ML-KEM 解封装在卡片上执行。

## PIN 和触摸策略

CanoKey 私钥支持每个密钥独立的 PIN 策略（never、once、always）和触摸策略
（never、always、cached）。默认策略是 9E 槽 PIN-never，其余支持的 PIV 密钥槽
PIN-once；触摸默认 never。PIN-never 密钥无需登录即可使用。PIN-once 和
PIN-always 密钥需要用户 PIN；PIN-always 签名和解密还需要新的逐操作授权，
不应假定一次登录永久有效。

## 与常见应用协同工作

### OpenSC

OpenSC 可以直接加载模块。请使用对应平台的构建产物，例如 Linux 上的
`libcanokey-pkcs11.so` 或 Windows 上的 `canokey-pkcs11.dll`：

```bash
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --list-slots
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --slot-index 0 --list-objects
pkcs11-tool --module /path/to/libcanokey-pkcs11.so --slot-index 0 --login --list-objects
```

签名或解密时，使用 `--list-objects` 和 `--mechanism-list` 报告的对象 ID 与机制。
PKCS#11 槽位索引不是 PIV 槽位编号，不能假定槽位索引 `2` 就是 PIV `9D`。

### OpenSSL

OpenSSL 本身不会直接加载 PKCS#11 模块。请使用 PKCS#11 provider（旧版 OpenSSL
可使用兼容的 engine），再通过 PKCS#11 URI 引用 PIV 密钥。典型 provider 流程如下：

```bash
openssl list -providers
openssl dgst -sha256 \
  -sign 'pkcs11:token=CanoKey;object=Digital%20Signature' \
  -out signature.bin message.bin
```

具体 URI 属性和命令行选项由 provider 配置决定。私钥操作会发送到卡片；证书解析、
哈希和公钥验证仍由 OpenSSL 执行。

### Adobe Acrobat

Acrobat 可以通过 PKCS#11 安全设备使用证书签署 PDF。在 Acrobat 的签名或安全设备
设置中添加 CanoKey 模块，让 Acrobat 枚举 PIV 证书，然后在签名时选择对应证书。
具体行为取决于 Acrobat 版本及其支持的算法。RSA 和 NIST EC 证书的兼容性最好；
Ed25519、X25519、SM2 和后量子 PIV 密钥通常不能用于 Acrobat 的 PDF 签名格式。

### 其他应用

Firefox 以及其他支持 PKCS#11 模块的软件，可以使用同一模块进行证书选择和 TLS
客户端认证，但最终能力取决于应用自身支持的算法。GnuPG 通常通过 `scdaemon`
使用 OpenPGP 应用，不会自动使用这个 PIV PKCS#11 模块。

## Standalone 与 managed

大多数应用使用 standalone 模式，由模块自行发现和管理 PC/SC 读卡器。Windows
minidriver 使用 managed 模式，因为 Windows 已经持有卡句柄；普通应用不需要开启
managed 模式。这是 minidriver 的实现细节。Windows 的行为说明参见
[Windows Minidriver](minidriver/)。

构建与安装说明参见 [canokey-pkcs11 仓库](https://github.com/canokeys/canokey-pkcs11)。
