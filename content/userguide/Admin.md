+++
title = "Admin"
date =  2020-12-27T02:30:15+08:00
weight = 10
+++

## Web Console

You may use the Admin panel in web console to control some behavior of the key.

<https://console.canokeys.org>

## Default values

* PIN: default 123456
* LED: default ON
* Keyboard: default OFF

## Reset

**Make sure that your admin applet has been locked, that is no "retries left" in the prompt message.**
Then go to the APDU console and send `00500000055245534554`.
Note that the LED of CanoKey will flash.
When it is flashing, touch the pad.
Repeat that until it stops flashing.

**All data will be erased**

确定PIN验证失败的消息中没有 retries left，然后直接进入 APDU console，输入 00500000055245534554 并发送。
CanoKey 的 LED 灯会闪烁，摸触摸盘。
反复直到它不再闪烁即可。

**所有数据会被清空**
