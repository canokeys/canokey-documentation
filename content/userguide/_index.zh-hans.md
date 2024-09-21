+++
title = "使用指南"
date = 2020-07-02T23:06:11+08:00
weight = 10
chapter = true
pre = "<b>1. </b>"
+++

# 第一章

## 使用指南

快速使用指南

## 默认密码

在使用 CanoKey 过程中，用户将遇到多种 PIN。本页列举了全部默认密码和说明，旨在帮助用户区分不同 PIN。


| PIN 名称 | 默认值 | 说明 |
| :------- | :----: | :--- |
| Admin PIN | 123456 | 用于管理 CanoKey 上的不同应用，如重置应用、修改NDEF配置等。 |
| FIDO2 PIN | 无默认值 | 部分强制使用 PIN 的 FIDO2 应用会询问此密码。FIDO2 PIN 没有预设值。用户在首次使用强制 PIN 的 FIDO2 应用时会收到设置 FIDO2 PIN 的提示，此时用户可以自己设置该 PIN。 |
| OpenPGP PIN | 123456 | 用于常规 OpenPGP 操作，如 OpenPGP 签名等。 |
| OpenPGP Admin PIN | 12345678 | 用于 OpenPGP 应用的管理操作。例如，在 CanoKey 上生成 OpenPGP 密钥对，或者修改 OpenPGP 密钥属性时，会需要该 PIN。 |
| PIV PIN | 123456 | 用于常规 PIV 操作，例如 PIV 身份认证、通过 PKCS#11 调用 PIV 进行签名等。 |
| PIV PUK | 12345678 | 用于在 PIV PIN 被锁定后解锁 PIV PIN。 |
