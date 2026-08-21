+++
title = "管理凭据"
date = 2022-01-03T18:59:12+08:00
weight = 2
+++

可以使用 CanoKey Console 或 `ckman` 管理 OATH 凭据。

## CanoKey Console

CanoKey Console 的网页版需要 Chrome 或 Chromium 内核浏览器。

1. 打开 CanoKey Console 的 [OATH 页面](https://console.canokeys.org/oath)，连接 CanoKey。
2. 点击页面顶部的 `+`，选择扫码添加、扫描屏幕上的二维码或手动添加。
3. 手动添加时，填写签发者、账户、密钥、类型、算法和位数等信息，然后确认添加。

TOTP 会自动显示；需要触摸确认的 TOTP，点击触摸图标后再触摸 CanoKey。查看 HOTP 时，点击对应凭据旁的刷新图标。

{{% notice warning %}}
HOTP 每次计算后计数器都会递增，请勿重复计算。
{{% /notice %}}

## ckman

安装 [CanoKey Manager](https://github.com/canokeys/yubikey-manager) 后，可以使用 `ckman` 管理 OATH 凭据。以下示例通过名称中包含 `Canokeys` 的智能卡读卡器连接设备；如果系统显示的读卡器名称不同，请相应修改 `--reader` 参数。

### 导入 `otpauth://` URI

如果身份验证服务提供了 `otpauth://` URI，可以直接导入：

```sh
ckman --reader "Canokeys" oath accounts uri "otpauth://totp/EXAMPLE.COM:username?secret=SOMESECRET&issuer=EXAMPLE.COM&algorithm=SHA1&digits=6&period=30"
```

### 使用参数添加凭据

也可以分别指定凭据参数。下面的命令添加一个使用 SHA-1、6 位数字和 30 秒周期的 TOTP 凭据：

```sh
ckman --reader "Canokeys" oath accounts add \
  --oath-type TOTP \
  --algorithm SHA1 \
  --digits 6 \
  --period 30 \
  --issuer "EXAMPLE.COM" \
  "username" "SOMESECRET"
```

使用 `ckman oath accounts add --help` 可以查看 HOTP、触摸确认和其他可用参数。

### 列出凭据

列出设备中的 OATH 凭据：

```sh
ckman --reader "Canokeys" oath accounts list
```

### 计算动态密码

计算所有 TOTP 凭据的动态密码：

```sh
ckman --reader "Canokeys" oath accounts code
```

也可以提供名称以计算指定凭据：

```sh
ckman --reader "Canokeys" oath accounts code "EXAMPLE.COM:username"
```

### 删除凭据

```sh
ckman --reader "Canokeys" oath accounts delete "EXAMPLE.COM:username"
```
