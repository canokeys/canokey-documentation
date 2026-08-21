+++
title = "常见问题"
date = 2020-07-04T16:19:06+08:00
weight = 3
+++

## GnuPG 和 PC/SC 冲突

GnuPG 默认使用自己的实现（[scdaemon](https://www.gnupg.org/documentation/manuals/gnupg/Invoking-SCDAEMON.html)）访问包括 CanoKey 在内的智能卡，这一实现与 PC/SC 冲突。详情请见：<https://ludovicrousseau.blogspot.com/2019/06/gnupg-and-pcsc-conflicts.html>。

为了避免冲突，我们建议使用 PC/SC 接口访问 CanoKey，即在 `scdaemon.conf` 中增加：

```
disable-ccid
```

Linux 和 macOS 中，这一文件通常位于 `~/.gnupg/scdaemon.conf`。

Windows 中通常不会遇到这一问题，如有必要，请修改 GnuPG 安装目录下的 `scdaemon.conf` 文件。

## PC/SC 占用

由于 PC/SC 对智能卡的访问可能是独占的（取决于应用程序访问模式），因此即使配置正确，GnuPG 仍然可能无法正确访问 CanoKey。如果遇到这一问题，只需重新插拔 CanoKey 即可。

常见占用 PC/SC 的程序包括：

* Firefox：可以在 “Preferences > Privacy & Security > Certificates” 中卸载（unload）“OpenSC Smartcard framework”。
