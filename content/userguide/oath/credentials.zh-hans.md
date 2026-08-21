+++
title = "凭据"
date = 2022-01-03T18:59:12+08:00
weight = 1
+++

CanoKey 上的 OATH 凭据由名称（通常为 `签发者:账户`）、密钥以及一组决定动态密码生成方式的参数组成。

## 凭据类型

| 类型 | 说明 |
| ---- | ---- |
| HOTP | 基于 HMAC 的一次性密码。密码由内部计数器生成，每计算一次，计数器递增一次。 |
| TOTP | 基于时间的一次性密码。密码由当前时间生成，时间周期可配置（通常为 30 秒）。 |

## 算法

| 算法        | 说明 |
| ----------- | ---- |
| HMAC-SHA1   | 支持最广泛的算法，是多数服务的默认选择。 |
| HMAC-SHA256 | 更安全的替代算法，部分服务支持。 |

## 位数

生成的动态密码位数可按凭据配置，最常见的是 6 位。

## 触摸确认

凭据可以带有触摸确认（require touch）属性。设置后，为该凭据生成密码时必须实际触摸 CanoKey，从而为每次密码计算确认用户在场。

## `otpauth://` URI 格式

许多服务以 `otpauth://` URI 的形式提供 OATH 凭据，可能是文本，也可能编码在二维码中。格式如下：

```
otpauth://TYPE/LABEL?secret=SECRET&issuer=ISSUER&algorithm=ALGORITHM&digits=DIGITS&period=PERIOD
```

* `TYPE` 为 `hotp` 或 `totp`。
* `LABEL` 标识凭据，通常为 `签发者:账户` 的形式。
* `secret` 为共享密钥，采用 Base32 编码。
* `issuer`、`algorithm`、`digits` 和 `period` 指定相应的凭据参数。

`otpauth://` URI 可以直接用 `ckman` 导入，参见[管理凭据](usage/)。

{{% notice note %}}
与 Yubico 的 OATH 实现不同，CanoKey 不支持对 OATH applet 的密码保护访问：没有访问密码，也没有解锁步骤。任何能访问设备的人都可以为不需要触摸确认的凭据计算密码。
{{% /notice %}}
