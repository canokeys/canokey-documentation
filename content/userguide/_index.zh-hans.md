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


| PIN 名称 | 默认值        | 说明        |
| :------- | :-----------: | :---------- |
| Admin PIN | 123456| 用于在 web console 上管理 CanoKey 上的不同应用，如，重置应用，修改触摸策略等。 |
| FIDO2 PIN |  | 部分强制使用 PIN 的 FIDU2/U2F 应用会询问此密码。FIDO2 PIN 没有预设值。用户在首次使用强制 PIN 的 FIDO2/U2F 应用时会收到设置 FIDO2 PIN 的提示，此时用户可以自己设置该 PIN。 |
| GPG PIN | 123456 | 用于常规 GPG 操作，如 GPG 签名等。 |
| GPG Admin PIN | 12345678 | 用于 GPG 应用的管理操作。例如，在 CanoKey 上生成 GPG 密钥对，或者修改 GPG 密钥属性时，会需要该 PIN。 |
| PIV PIN | 123456 | 用于常规 PIV 操作，例如 PIV 身份认证、通过 PKCS#11 调用 PIV 进行签名等。 |
| PIV PUK | 12345678 | 用于在 PIV PIN 被锁定后的解锁操作。 |

