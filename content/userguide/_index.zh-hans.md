+++
title = "使用指南"
date = 2020-07-02T23:06:11+08:00
weight = 10
chapter = true
pre = "<b>一、</b>"
+++

# 第一章

## 名词表

- **FIDO**: Fast Identity Online，是一个开放的行业联盟，致力于制定和推广无密码身份验证标准。
- **U2F**: Universal 2nd Factor，是 FIDO 联盟的一个标准协议，通过 USB 设备等外部介质提供双因素身份验证，增强账号安全性。该协议已被 CTAP 2.0 / 2.1 取代。CanoKey 支持 U2F 及 CTAP 2.0 / 2.1 协议。
- **WebAuthn**: Web Authentication，是一种由 W3C 定义的身份认证协议，它的设备侧实现包括 CTAP / U2F 协议。
- **Passkey**: Passkey 是 WebAuthn 的一种实现。CanoKey 可以通过 CTAP 协议存储 Passkey 密钥。
- **OpenPGP**: Open Pretty Good Privacy，是一种用于加密和签署数据的开放标准协议，广泛用于电子邮件等通信的安全保障。CanoKey 支持 OpenPGP 3.4.1 中的所有必须功能。
- **GnuPG**: Gnu Privacy Guard，是一个实现 OpenPGP 标准的自由软件工具，用于加密和签署数据。
- **PIV**: Personal Identity Verification，是美国政府制定的身份认证标准，用于增强个人身份验证的安全性。CanoKey 支持 PIV 标准中的所有必须功能和一些扩展功能。
- **NDEF**: NFC Data Exchange Format，是一种用于在 NFC 设备之间交换数据的标准格式。CanoKey 支持模拟符合 NDEF 格式的 NFC 标签。
- **OTP**: One-Time Password，一次性密码，用于单次认证，增强安全性，常用于双因素认证。常见的 OTP 包括 HOTP 和 TOTP。CanoKey 支持存储这两种 OTP 密钥。
- **HOTP**: HMAC-based One-Time Password，基于 HMAC（Hash-based Message Authentication Code）算法生成的一次性密码。
- **TOTP**: Time-based One-Time Password，基于时间的动态一次性密码，通过当前时间和一个共享密钥生成。
- **WebUSB**: 一种允许网页与 USB 设备直接通信的协议，简化了设备连接和数据传输。CanoKey 支持通过 WebUSB 完成配置。

## 默认 PIN

在使用 CanoKey 过程中，用户将遇到多种 PIN。本页列举了全部默认 PIN。


| PIN 名称 | 默认值 | 说明 |
| :------- | :----: | :--- |
| Admin PIN | 123456 | 用于管理 CanoKey 上的不同应用，如重置应用、修改NDEF配置等。 |
| FIDO2 PIN | 无默认值 | 部分强制使用 PIN 的 FIDO2 应用会询问此口令。FIDO2 PIN 没有预设值。用户在首次使用强制 PIN 的 FIDO2 应用时会收到设置 FIDO2 PIN 的提示，此时用户可以自己设置该 PIN。 |
| OpenPGP PIN | 123456 | 用于常规 OpenPGP 操作，如 OpenPGP 签名等。 |
| OpenPGP Admin PIN | 12345678 | 用于 OpenPGP 应用的管理操作。例如，在 CanoKey 上生成 OpenPGP 密钥对，或者修改 OpenPGP 密钥属性时，会需要该 PIN。 |
| PIV PIN | 123456 | 用于常规 PIV 操作，例如 PIV 身份认证、通过 PKCS#11 调用 PIV 进行签名等。 |
| PIV PUK | 12345678 | 用于在 PIV PIN 被锁定后解锁 PIV PIN。 |
