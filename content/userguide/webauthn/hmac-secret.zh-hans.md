+++
title = "HMAC-secret 扩展"
date = 2022-06-08T20:30:05+08:00
weight = 6
+++

CanoKey 支持 HMAC-secret 扩展，允许应用程序从密钥派生出秘密。

## systemd-cryptenroll

- [systemd-cryptenroll](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html)，用于 LUKS 全盘加密

{{% notice note %}}
受 CTAP 实现中的一处 [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) 影响，固件版本小于等于 `1.3` 的 CanoKey 与 libfido2 1.7.0 不兼容，因此不能用于 `systemd-cryptenroll`。
受影响的用户请使用 libfido2 1.6.0。
{{% /notice %}}
