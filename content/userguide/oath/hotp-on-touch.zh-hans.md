+++
title = "HOTP 触摸输入"
date = 2022-01-03T18:59:12+08:00
weight = 4
+++

CanoKey 可以模拟键盘，在触摸时输入 HOTP 动态密码。要启用此功能，请在 CanoKey Console 中完成以下设置：

1. 打开 [Admin 页面](https://console.canokeys.org/admin)并连接 CanoKey。
2. 点击 `AUTHENTICATE`，输入 Admin PIN 完成认证。
3. 在 `Config` 中启用 `HOTP on touch`。
4. 打开 [OATH 页面](https://console.canokeys.org/oath)，点击目标 HOTP 凭据旁的星形图标，将其设为默认凭据。
5. 断开连接，然后重新插入 CanoKey。

设置完成后，触摸 CanoKey 即可输入默认 HOTP 凭据生成的动态密码。
