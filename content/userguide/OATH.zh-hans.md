+++
title = "OATH"
date =  2022-01-03T18:59:12+08:00
weight = 35
+++

OATH 是一个提供开放认证标准的[组织](https://openauthentication.org/)：基于时间的一次性密码 (TOTP) 和基于 HMAC 的一次性密码 (HOTP)。

HOTP 和 TOTP 都在 CanoKey Pigeon 和 CanoKey epoxy 版本中实现。CanoKey 最多可容纳 100 个 OATH 令牌。

## 固件版本 1.5 及更新版本（例如 CanoKey Pigeon）

应使用 4.0 或更高版本的 `ykman` 命令配置 OATH 和读取 OATH 令牌。
### 设置

如果身份验证提供商为您提供了 URI `otpauth://totp/username@EXAMPLE.COM:12345678-90ab-cdef-1234-567890abcdef?digits=6&secret=SOMESECRET&period=30&algorithm=SHA1&issuer=username%40EXAMPLE.COM`，您应该通过以下方式进行配置
```
ykman -r "Canokeys" oath accounts uri "otpauth://totp/username@EXAMPLE.COM:12345678-90ab-cdef-1234-567890abcdef?digits=6&secret=SOMESECRET&period=30&algorithm=SHA1&issuer=username%40EXAMPLE.COM"
```

或者，如果分别提供字段，包括 base32 编码的密文（在本例中为 `SOMESECRET`）、算法（在本例中为 SHA1）、数字长度，则可以通过以下方式设置

```
ykman -r "Canokeys" oath accounts add -o TOTP -d 6 -a SHA1 -i 'username@EXAMPLE.COM' -P 30 USERNAME SOMESECRET
```

您可以使用 `ykman oath accounts add --help` 查找所有可用选项。

### 读取 OATH 令牌

您可以使用 [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/) 读取 OTP。

* 打开 Yubico Authenticator，点击左上角的按钮启动侧边菜单。
* 点击 'Settings'，然后点击 'Custom reader'
* 选择 "Enable custom reader' and fill in 'Canokey"，并在 "自定义阅读器过滤器 "Custom reader filter"。
* 点击 'Save'
* 点击顶部的"<"返回设置。
* 拔下并插入 CanoKey
* 单击左上角按钮切换到 'Authenticator'

然后您就会看到所有 OATH 令牌的列表。

## Firmware version older than 1.5 (for example, CanoKey epoxy edition)

You should use [CanoKey Web Console](https://console.canokeys.org) on a Chromium-based web browser to configure OATH.

### Setup

* Go to [OATH Applet](https://console.canokeys.org/oath) of the web console and connect your CanoKey.
* Click 'CONNECT' on the top right corner
* Select your CanoKey from the prompt dialog
* Click the '+' sign on the down left of the box, then the 'Add credential to OATH Applet' section will show up on the web page.
* Fill in the details, **or** copy the URI you got and click 'IMPORT OTPAUTH FROM CLIPBOARD'.
* Click ADD
* If there is no error, there will be a green box with 'Add OATH credential success' shown in the bottom left of your web page.

### Read OATH TOTP token

* Go to [OATH Applet](https://console.canokeys.org/oath) of the web console and connect your CanoKey.
* Click 'CONNECT' on the top right corner
* Select your CanoKey from the prompt dialog
* Click the 'Calculate TOTP' button on the line of TOTP you want to calculate.
* If there is no error, there will be a green box with 'TOTP code is xxxxxx' shown in the bottom left of your web page.


## 可选：为 HOTP 启用触摸输入

如果您正在使用 HOTP，并希望让 CanoKey 在您每次触摸按键时都能键入 HOTP 令牌，您应该  

* 进入网络控制台的[Admin Applet](https://console.canokeys.org/admin)，连接 CanoKey。
* 如果尚未将 CanoKey 与网络控制台连接，请单击右上角的 "CONNECT"，然后在提示对话框中选择您的 CanoKey。
* 单击 "AUTHENTICATE"并输入管理员小程序密码，以管理员用户身份进行认证
* 在 " Config"部分启用 "HOTP on touch"，然后就会在网页左下方看到一个绿色方框，上面显示 "HOTP on touch is on"。
* 进入网络控制台的[OATH Applet](https://console.canokeys.org/oath)
* 点击要使用的 HOTP 行上的星形图标"٭"。这将使该 HOTP 标记成为默认值
* 点击右上角的DISCONNECT
* 拔下并重新插入 CanoKey。
现在您可以按触摸区域键入默认的 HOTP 令牌。

