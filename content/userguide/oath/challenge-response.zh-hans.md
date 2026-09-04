+++
title = "质询-响应"
date = 2022-01-03T18:59:12+08:00
weight = 3
+++

固件 3.1.1 及更高版本提供两个 HMAC-SHA1 质询-响应（challenge-response）槽位，供 [KeePassXC](https://keepassxc.org/) 等使用质询-响应解锁数据库的应用使用。

主机可通过 OATH/PCSC 命令发送最长 64 字节的质询，设备将返回原始的 20 字节 HMAC-SHA1 响应。

质询-响应槽位中的密钥只能写入，无法通过配置接口读出。
