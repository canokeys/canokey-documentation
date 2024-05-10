+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 20
+++

[OpenPGP](https://www.openpgp.org/) is a standard for signing and encrypting defined as [RFC4880](https://tools.ietf.org/html/rfc4880). It uses a private key to sign / encrypt messages and documents. One of the most commonly used tools for using OpenPGP is GNU Privacy Guard, which is short as GnuPG or GPG.

The private key can be stored in CanoKey, or you can use CanoKey to generate a OpenPGP key. The private key stored in CanoKey cannot be read out. This reduced the chance of leakage of the private key.

## Supported algorithm

* RSA2048
* RSA4096\*
* X25519
* Ed25519
* NIST P-256 (secp256r1, prime256v1)
* NIST P-384 (secp384r1)
* secp256k1

\* Due to the limitation of computing performance, RSA4096 cannot be generated in the card. However, you can generate the key pair and import it to CanoKey.

Please note that RSA3072 and Curve25519 are not supported at this time, and RSA4096 will not display a counter when decrypted.

## Default values

* PIN: default 123456, min len 6, max len 64
* Admin PIN: default 12345678, min len 8, max len 64
* Reset Code: default not set, min len 8, max len 64
* Signature PIN : forced // namely verify pin every signature
* Touch Policy: OFF for SIG, DEC, AUT
* Touch Cache Time: 0

## Touch Policy

There are three key slots for OpenPGP, namely Signature key (SIG), Encryption key (DEC) and Authentication key (AUT). You may turn ON or OFF touch policies for SIG, DEC, AUT in the admin applet in the web console or via the `gpg` command. The value of touch cache time is in between 0 and 255 seconds (0 means no cache).

### Firmware Version <= 1.4

Touch policy is configured through the admin applet. The technical details can be found in [https://docs.canokeys.org/development/protocols/admin/](https://docs.canokeys.org/development/protocols/admin/).

### Firmware Version >= 1.5

Touch policy is implemented using the User Interaction Flag (Part 4.4.3.6 of the OpenPGP specification). Use a recent GnuPG to configure it.

**Touch policy is only applicable when using the USB interface.**

## PIN Policy

For DEC and AUT, after successfully verifying the PIN, it is always valid for the whole power-up.

For SIG, if the flag `forcesig` is on, the PIN is requested for each signature; otherwise, PIN is only requested for the first signature after power-up.

## Using GnuPG

Please refer to the [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html) at the moment.

You may also refer to [https://github.com/drduh/YubiKey-Guide](https://github.com/drduh/YubiKey-Guide).

### Cheatsheet

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

Note that we recommend using `ccid` and `pcsclite` for `gpg-agent`/`scdaemon` to access CanoKey, namely in `~/.gnupg/scdaemon.conf`

```
pcsc-driver /usr/lib/libpcsclite.so
card-timeout 5
disable-ccid
```

You should setup `ccid` as in [setup](https://docs.canokeys.org/userguide/setup/).

## Debug and Report

### Linux

You may use `pcsc_scan` to check whether the smart card is detected by `pcscd`. Note that `pcscd` only uses the first smart card it detects, hence if you have other smart card readers in your box, you should remove or disable them first.

You can use `pcscd -a -d -f` to monitor the status of card reader and the communication. The log of it may be reported for troubleshooting.

## Preemption problem

If you get output below when use OpenPGP SmartCard function,

```
gpg: selecting card failed: No such device
gpg: OpenPGP card not available: No such device
```
Check that you have browsers (including Electron and thunderbird) opened and you are using Windows or installed OpenSC in Linux.

If you are sure, uninstall OpenSC (Linux only) or disable the PIV feature of Canokey (if you don't use PIV), and it will be fixed. Or if it's Firefox, you can enter "Firefox > Preferences > Privacy&Security > Certificates" and you can see "OpenSC Smartcard framework". You can click on it and click "unload"

Another solution is to add `pcsc-shared` in `scdaemon.conf`. But it will lead to asking PIN every time when you use GnuPG 2.4+.
