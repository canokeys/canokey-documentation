+++
title = "OTP"
date =  2022-01-03T18:59:12+08:00
weight = 35
+++

OATH 是一个提供开放认证标准的[组织](https://openauthentication.org/)：基于时间的一次性密码 (TOTP) 和基于 HMAC 的一次性密码 (HOTP)。

Canary、Pigeon 和 Epoxy 均实现了 HOTP 和 TOTP。

3.0.x 及更早版本的固件最多可保存 100 个 OATH 凭据。固件版本 3.1.1 起不再固定凭据数量上限，实际可保存的数量取决于设备的可用存储空间；空间不足时将无法继续添加凭据。

固件 3.1.1 及更高版本还提供两个用于 HMAC-SHA1 质询-响应（challenge-response）的槽位，可供 KeePassXC 等应用使用。主机可通过 OATH/PCSC 命令发送最长 64 字节的质询，设备将返回原始的 20 字节 HMAC-SHA1 响应。密钥只能写入，无法通过配置接口读出。

## 配置和使用 OATH

可以使用 CanoKey Console 或 `ckman` 管理 OATH 凭据。

### 使用 CanoKey Console

CanoKey Console 的网页版需要 Chrome 或 Chromium 内核浏览器。

1. 打开 CanoKey Console 的 [OATH 页面](https://console.canokeys.org/oath)，连接 CanoKey。
2. 点击页面顶部的 `+`，选择扫码添加、扫描屏幕上的二维码或手动添加。
3. 手动添加时，填写签发者、账户、密钥、类型、算法和位数等信息，然后确认添加。

TOTP 会自动显示；需要触摸确认的 TOTP，点击触摸图标后再触摸 CanoKey。查看 HOTP 时，点击对应凭据旁的刷新图标。HOTP 每次计算后计数器都会递增，请勿重复计算。

### 使用 ckman

安装 [CanoKey Manager](https://github.com/canokeys/yubikey-manager) 后，可以使用 `ckman` 管理 OATH 凭据。以下示例通过名称中包含 `Canokeys` 的智能卡读卡器连接设备；如果系统显示的读卡器名称不同，请相应修改 `--reader` 参数。

如果身份验证服务提供了 `otpauth://` URI，可以直接导入：

```sh
ckman --reader "Canokeys" oath accounts uri "otpauth://totp/EXAMPLE.COM:username?secret=SOMESECRET&issuer=EXAMPLE.COM&algorithm=SHA1&digits=6&period=30"
```

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

列出设备中的 OATH 凭据：

```sh
ckman --reader "Canokeys" oath accounts list
```

计算所有 TOTP 凭据的动态密码：

```sh
ckman --reader "Canokeys" oath accounts code
```

也可以提供名称以计算指定凭据，或删除指定凭据：

```sh
ckman --reader "Canokeys" oath accounts code "EXAMPLE.COM:username"
ckman --reader "Canokeys" oath accounts delete "EXAMPLE.COM:username"
```

使用 `ckman oath accounts add --help` 可以查看 HOTP、触摸确认和其他可用参数。

## 可选：为 HOTP 启用触摸输入

如果希望 CanoKey 在触摸时输入 HOTP，请在 CanoKey Console 中完成以下设置：

1. 打开 [Admin 页面](https://console.canokeys.org/admin)并连接 CanoKey。
2. 点击 `AUTHENTICATE`，输入 Admin PIN 完成认证。
3. 在 `Config` 中启用 `HOTP on touch`。
4. 打开 [OATH 页面](https://console.canokeys.org/oath)，点击目标 HOTP 凭据旁的星形图标，将其设为默认凭据。
5. 断开连接，然后重新插入 CanoKey。

设置完成后，触摸 CanoKey 即可输入默认 HOTP 凭据生成的动态密码。
