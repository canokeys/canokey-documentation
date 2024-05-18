+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/)  是 [RFC4880](https://tools.ietf.org/html/rfc4880) 规定的签名和加密标准。它使用私人密钥来签署/加密信息和文件。使用 OpenPGP 的最常用工具之一是 GNU Privacy Guard，简称 GnuPG 或 GPG。

私钥可以存储在 CanoKey 中，也可以使用 CanoKey 生成 OpenPGP 密钥。存储在 CanoKey 中的私钥无法读出。这就减少了私钥泄漏的可能性。

## 支持的算法

* RSA2048
* RSA3072
* RSA4096
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* secp256k1

## 默认值

* PIN: 默认为 123456, 最小长度为 6, 最大长度为  64
* Admin PIN: 默认为  12345678, 最小长度为  8, 最大长度为  64
* Reset Code: 默认没有设置，最小长度为  8, 最大长度为  64
* Signature PIN : forced // 即每次签名都要验证密码
* 触摸策略：SIG, DEC, AUT 为 OFF
* 触摸缓存时间：0

## 触摸策略

OpenPGP 有三个密钥插槽，即签名密钥 (SIG)、加密密钥 (DEC) 和验证密钥 (AUT)。您可以在网络控制台的管理小程序中或通过 `gpg` 命令打开或关闭 SIG、DEC 和 AUT 的触摸策略。触摸缓存时间的值在 0 至 255 秒之间（0 为无缓存）。
### 固件版本  <= 1.4

Touch policy is configured through the admin applet. The technical details can be found in [https://docs.canokeys.org/development/protocols/admin/](https://docs.canokeys.org/development/protocols/admin/).

### 固件版本  >= 1.5

触摸策略是通过 the User Interaction Flag（OpenPGP 规范第 4.4.3.6 部分）实现的。请使用最新的 GnuPG 进行配置。

**触摸策略仅适用于使用 USB 接口时。**

## PIN 策略

对于 DEC 和 AUT，在成功验证 PIN 码后，PIN 码在整个开机过程中始终有效。

对于 SIG，如果 flag `forcesig` 打开，则每次签名都要求输入 PIN；否则，只在开机后的第一次签名时要求输入 PIN。

## 使用 GnuPG

目前请参考 [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html)

您也可以参考 [https://github.com/drduh/YubiKey-Guide](https://github.com/drduh/YubiKey-Guide).

### 备忘录

```
# card related
# try below to make sure gpg works with canokey
gpg --card-status
# use this for editting card info and config
# and/or generating keys
gpg --edit-card
# generate key
gpg --expert --full-generate-key
# get key infos
gpg --list-keys --with-fingerprint --with-subkey-fingerprint [keyid or user id]
gpg --list-keys --with-keygrip [keyid or user id]
gpg --list-sigs [keyid or user id]
# edit key
# add uid/subkey in the interactive shell
# keytocard or addcardkey
gpg --edit-key <keyid or user id>
# import/export key
gpg --import file
gpg --armor --output file --export <keyid or user id>
gpg --armor --output file --export-secret-keys <keyid or user id>
gpg --delete-keys <keyid or user id>
# sign and verify
gpg --armor --sign file
gpg --sign-key --ask-cert-level <key id>
gpg --armor --detach-sign file
gpg --clear-sign file
gpg --verify file.asc
# encrypt and decrypt
gpg --armor --encrypt --recipient <keyid or user id>
gpg --decrypt file
# misc
gpgconf --kill gpg-agent
gpg-connect-agent reloadagent /bye
gpgconf --list-dirs agent-socket
gpgconf --list-dirs agent-extra-socket
gpgconf --list-dirs agent-ssh-socket
```

### Linux

请注意，我们建议在 `gpg-agent/scdaemon` 中使用 `ccid` 和 `pcsclite` 来访问 CanoKey，即在 `~/.gnupg/scdaemon.conf` 中使用

```
pcsc-driver /usr/lib/libpcsclite.so
card-timeout 5
disable-ccid
```

您应该像[setup](https://docs.canokeys.org/userguide/setup/)中那样设置 `ccid`

## Debug and Report

### Linux

You may use `pcsc_scan` to check whether the smart card is detected by `pcscd`. Note that `pcscd` only uses the first smart card it detects, hence if you have other smart card readers in your box, you should remove or disable them first.

You can use `pcscd -a -d -f` to monitor the status of card reader and the communication. The log of it may be reported for troubleshooting.

## 抢占问题

使用 OpenPGP 智能卡功能时，如果得到以下输出结果、

```
gpg: selecting card failed: No such device
gpg: OpenPGP card not available: No such device
```
检查是否已打开浏览器（包括 Electron 和 thunderbird），是否使用 Windows 或在 Linux 中安装了 OpenSC。


如果确定的话，卸载 OpenSC（仅限 Linux）或禁用 Canokey 的 PIV 功能（如果不使用 PIV），问题就会解决。如果是 Firefox，则可以进入 "Firefox > Preferences > Privacy&Security > Certificates"，然后可以看到 "OpenSC Smartcard framework"。你可以点击它，然后点击 "unload"。

另一种解决方案是在 `scdaemon.conf` 中添加 `pcsc-shared`。但这将导致在使用 GnuPG 2.4+ 时每次都要询问 PIN 码。
