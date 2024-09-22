+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/) is a signature and encryption standard specified by [RFC4880](https://tools.ietf.org/html/rfc4880). This standard achieves information and file signing/encryption through the use of private keys. One of the commonly used OpenPGP tools is GNU Privacy Guard, often abbreviated as GnuPG or GPG. In Windows, you can also use [Kleopatra](https://www.openpgp.org/software/kleopatra/).

## Basic Information

### Supported Algorithms

* RSA2048
* RSA3072
* RSA4096
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* secp256k1

### Defaults

* PIN: Default is 123456, minimum length is 6, maximum length is 64
* Admin PIN: Default is 12345678, minimum length is 8, maximum length is 64
* Reset Code: Default is empty, minimum length is 8, maximum length is 64
* Signature PIN: forced (PIN verification required for each signature)
* Touch Policy: SIG, DEC, AUT are off
* Touch Cache Time: 0

### Touch Policy

{{% notice note %}}
Touch policy is only effective when using the USB interface.
{{% /notice %}}

OpenPGP supports up to 3 keys: signature key (SIG), encryption key (DEC), and authentication key (AUT). Depending on the firmware version, you can set the touch policy for SIG, DEC, and AUT in the CanoKey Console or via the `gpg` command. The value of touch cache time ranges from 0 to 255 seconds (0 means no cache).

#### Firmware Version <= 1.4

Please use the "Settings" application in the CanoKey Console to modify the touch policy.

#### Firmware Version >= 1.5

Please use GnuPG to modify the touch policy.

### PIN Policy

For DEC and AUT keys, after the PIN verification is successful, verification will not be required again until CanoKey is disconnected and reinserted.

For SIG, if `forcesig` is on, a PIN is required for each signature; otherwise, a PIN is only required for the first signature after power-on.

## Common Operations

## FAQs

### GnuPG and PC/SC Conflict

GnuPG, by default, uses its own implementation ([scdaemon](https://www.gnupg.org/documentation/manuals/gnupg/Invoking-SCDAEMON.html)) to access smart cards including CanoKey, which conflicts with PC/SC. For details, see: <https://ludovicrousseau.blogspot.com/2019/06/gnupg-and-pcsc-conflicts.html>.

To avoid conflicts, we recommend using the PC/SC interface to access CanoKey by adding the following to `scdaemon.conf`:

```
disable-ccid
```

In Linux and macOS, this file is usually located at `~/.gnupg/scdaemon.conf`.

In Windows, this issue is typically not encountered. If necessary, please modify the `scdaemon.conf` file under the GnuPG installation directory.

### PC/SC Occupancy

Since PC/SC access to smart cards may be exclusive (depending on the application access mode), even if configured correctly, GnuPG may still fail to access CanoKey. If you encounter this issue, simply re-plug CanoKey.

Programs commonly occupying PC/SC include:

* Firefox: You can unload "OpenSC Smartcard framework" in “Preferences > Privacy & Security > Certificates”.