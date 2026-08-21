+++
title = "FAQs"
date = 2020-07-04T16:19:06+08:00
weight = 3
+++

## GnuPG and PC/SC Conflict

GnuPG, by default, uses its own implementation ([scdaemon](https://www.gnupg.org/documentation/manuals/gnupg/Invoking-SCDAEMON.html)) to access smart cards including CanoKey, which conflicts with PC/SC. For details, see: <https://ludovicrousseau.blogspot.com/2019/06/gnupg-and-pcsc-conflicts.html>.

To avoid conflicts, we recommend using the PC/SC interface to access CanoKey by adding the following to `scdaemon.conf`:

```
disable-ccid
```

In Linux and macOS, this file is usually located at `~/.gnupg/scdaemon.conf`.

In Windows, this issue is typically not encountered. If necessary, please modify the `scdaemon.conf` file under the GnuPG installation directory.

## PC/SC Occupancy

Since PC/SC access to smart cards may be exclusive (depending on the application access mode), even if configured correctly, GnuPG may still fail to access CanoKey. If you encounter this issue, simply re-plug CanoKey.

Programs commonly occupying PC/SC include:

* Firefox: You can unload "OpenSC Smartcard framework" in “Preferences > Privacy & Security > Certificates”.
