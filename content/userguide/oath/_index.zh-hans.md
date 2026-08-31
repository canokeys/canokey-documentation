+++
title = "OTP"
date = 2022-01-03T18:59:12+08:00
weight = 35
+++

OATH 是一个提供开放认证标准的[组织](https://openauthentication.org/)：基于时间的一次性密码 (TOTP) 和基于 HMAC 的一次性密码 (HOTP)。这些标准通常用于双因素认证中生成一次性动态密码。

Canary、Pigeon 和 Epoxy 均实现了 HOTP 和 TOTP。

## 存储容量

CanoKey 最多可保存 100 个 OATH 凭据。

## 本章内容

* [凭据](credentials/) — 凭据类型、算法、触摸确认属性以及 `otpauth://` URI 格式。
* [管理凭据](usage/) — 使用 CanoKey Console 或 `ckman` 添加、列出、计算和删除凭据。
* [HOTP 触摸输入](hotp-on-touch/) — 配置 CanoKey 在触摸时输入 HOTP 动态密码。
