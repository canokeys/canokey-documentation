+++
title = "PIV"
date =  2020-07-11T22:33:15+08:00
weight = 25
+++

个人身份认证(PIV) 是由美利坚合众国政府标准定义为[FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf)的工具. PIV 使用智能卡储存用于签名和加密的密钥. PIV主要用于非web的场景.

## 基本信息
### 可支持的算法

* RSA2048
* NIST P-256
* NIST P-384

### 默认值

* PIN: 默认为123456
* PUK: 默认为12345678
* 管理密钥: 默认为010203040506070801020304050607080102030405060708

### 最大证书大小

* 固件版本 1.5 或更早: 1000B
* 固件版本 1.6.0 或更新: 3kB

## 用户指导

以上用户指导由 `yubico-piv-tool` version 2.2.1, `ykman` version 4.0.7 以及 `opensc` version 0.22.0 under Linux所编写.

### 初始PIV在您的卡上

通常来讲, 在您使用Canokey的PIV之前, 您应该初始化 Cardholder Unique Identifier (CHUID)以及Cardholder Capability Container (CCC)

```
yubico-piv-tool -r canokeys -a set-ccc
yubico-piv-tool -r canokeys -a set-chuid
```

### 生成密钥和证书

您可以使用 `yubico-piv-tool` 或 `ykman`。

#### 使用 `yubico-piv-tool`

在本例中，我们使用 NIST P-384 算法生成密钥。私钥保存在 Canokey 中，公钥将写入 `public384.pem`。

```
$ yubico-piv-tool -r canokeys -s 9a -a generate -A ECCP384 -o public384.pem
Successfully generated a new private key.
```

然后为该密钥生成证书。我们以 SSH 密钥为例，将证书导出为 cert.pem。

```
$ yubico-piv-tool -r canokeys -a verify-pin -a selfsign-certificate -s 9a -S "/CN=SSH key/" -i public384.pem -o certificate.pem
Enter PIN: 
Successfully verified PIN.
Successfully generated a new self signed certificate.
```

然后我们就可以导入证书.

```
$ yubico-piv-tool -r canokeys -a import-certificate -s 9a -i certificate.pem
Successfully imported a new certificate.
```

如果您想验证密钥是否存在，可以通过以下方式进行验证

```
$ yubico-piv-tool -r canokeys -a read-certificate  -s 9a
```

#### 使用 `ykman`

可适用于`ykman` version 4.0 或以上版本.同样地, 我们可以生成一个与 NIST P-384 algorithm密钥对.此公钥将被写进 `public384.pem`.

```
$ ykman -r canokeys piv keys generate -a ECCP384 9a public384.pem
```

然后为该密钥生成证书。证书将保存在 Canokey 中，因此无需像使用 `yubico-piv-tool` 那样再次导入。以 SSH 密钥为例，证书有效期为 3 年（`-d 1095`）。

```
$ ykman -r canokeys piv certificates generate -d 1095 -s "CN=SSH Key" 9a public384.pem
```

要在 Canokey 上验证您的证书，您可以尝试

```
$ ykman -r canokeys piv info
```

### 使用SSH进行身份验证

#### 使用 ssh-agent

使用 pkcs11 提供程序运行 `ssh-agent`。这里我们使用 OpenSC。在下面的示例中，OpenSC 库位于 /usr/lib64/opensc-pkcs11.so。

```
$ eval $(ssh-agent -P /usr/lib64/opensc-pkcs11.so)
Agent pid 61757
```

接着通过以下代码添加密钥

```
$ ssh-add -s /usr/lib64/opensc-pkcs11.so
Enter passphrase for PKCS#11: 
Card added: /usr/lib64/opensc-pkcs11.so
```

现在您可以检查您的SSH公钥.

```
$ ssh-add -L
ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBDydOf6U+9/hAknZnJckyFwoinXKVEjTZkVV7bKNDZs4XsaHUoQix3z3+LsVn9WsLKeAKtigv2GS/removed/Snip12345678901234567890123456789012/SnipSnip== PIV AUTH pubkey
```

用你喜欢的方式将打印好的密钥添加到远程机器上。现在，你可以像往常一样 ssh 到远程计算机。

#### 不使用 ssh-agent

如果没有 ssh-agent，每次 ssh 访问机器时都需要输入 PIV pin。

检查 SSH 公钥

```
ssh-keygen -D /usr/lib64/opensc-pkcs11.so  -e
```

接着以您喜欢的方式将公共密钥添加进您的远程操控机器. 现在通过提供PKCS#11库ssh到您的远程操控机器。

```
$ ssh -I /usr/lib64/opensc-pkcs11.so remote.lan
Warning: Permanently added 'remote.lan' (ED25519) to the list of known hosts.
Enter PIN for 'SSH key': 
X11 forwarding request failed on channel 0
Last login: Thu Jan 20 22:57:23 2022 from 192.168.1.246
[user@remote ~]$ 
```

#### Windows系统使用方法

为了在Windows上使用PIV SSH ,您可以尝试

- [OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) and [OpenSC](https://github.com/OpenSC/OpenSC), and the above instructions will work too. Or
- [WinCryptSSHAgent](https://github.com/buptczq/WinCryptSSHAgent)

{{% 注意事项 %}}
在Windows上使用 PIV SSH, `NIST P-256` 和 `NIST P-384` 可能会不兼容. 如果您依旧无法通过以上两种方法找到您的公钥, 请使用`RSA2048`. 
{{% /注意事项%}}

### 导入`.pfx` 密钥&证书

 `.pfx`文件 可能同时包含私钥和证书. 在此次试例中, 我们将导入 `credential.pfx` 到`9d`位置.

```
$ yubico-piv-tool -r canokeys -a verify-pin -s 9d -i "credential.pfx" -K PKCS12 -a import-key -a import-cert
```

## 重置PIV小程序

如果您的PIN和PUK都被封锁无法使用的情况下, 您可以使用`yubico-piv-tool -r canokeys -a reset` 已重置PIV小程序.在重置后, 所有的PIV数据都已从您的Canokey抹除. 以及您的PIN, PUK都已被恢复成默认状态. **在重置过后请记得再次初始化的PIV**.
