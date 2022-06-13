+++
title = "FIDO2/U2F"
date =  2022-06-08T20:30:05+08:00
weight = 15
+++

## 特性

CanoKey 的 FIDO2/U2F 功能遵循 [CTAP2.0](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) 以及 [CTAP1/U2F](https://fidoalliance.org/specs/fido-u2f-v1.0-ps-20141009/fido-u2f-hid-protocol-ps-20141009.html) 标准。

支持的特性有：

- 高达 64 组 resident keys。
- 支持 HMAC 扩展
- Ed25519 算法

## 多因素认证

CanoKey 可以用于很多[网站](https://2fa.directory/int/)的双因素认证。

## PIN

出厂时没有设置默认 PIN。如果需要，用户可以在 Windows Hello 或其他应用中设置新的 PIN。

## OpenSSH

可以通过下面的命令来生成 ssh 使用的私钥。

```
ssh-keygen -t ecdsa-sk
# or you prefer ed25519
ssh-keygen -t ed25519-sk
```

更多信息可以参考 [这里](https://undeadly.org/cgi?action=article;sid=20191115064850)。

## PAM

请使用 Yubico 提供的 `pam_u2f`。常见的应用场景是对 `sudo` 命令使用 FIDO2/U2F。

## HMAC-secret 扩展

可能用到的应用有：

- [khefin](https://github.com/mjec/khefin)， 用于 LUKS 全盘加密。
- [systemd v248+](http://0pointer.net/blog/unlocking-luks2-volumes-with-tpm2-fido2-pkcs11-security-hardware-on-systemd-248.html)，用于 LUKS 全盘加密

  {{% notice note %}}
  受 CTAP 实现中的一处 [bug](https://github.com/Yubico/libfido2/issues/322#issuecomment-817174671) 影响，固件版本小于等于 `1.3` 的 CanoKey 与 libfido2 1.7.0 不兼容，因此不能用于 `systemd-cryptenroll`。
  受影响的用户请使用 libfido2 1.6.0。
  {{% /notice %}}

- [Windows Hello](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)
