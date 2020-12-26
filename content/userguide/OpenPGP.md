+++
title = "OpenPGP"
date =  2020-07-04T16:19:06+08:00
weight = 5
+++

[OpenPGP](https://www.openpgp.org/) is a standard for signing and encrypting defined as [RFC4880](https://tools.ietf.org/html/rfc4880). It uses a private key to sign / encrypt messages and documents. One of the most commonly used tool for using OpenPGP is GNU Privacy Guard, which is short as GnuPG or GPG. So you might see some online materials also use GPG to denote the OpenPGP related techology.

The private key can be stored in CanoKey, or you can use CanoKey to generate a OpenPGP key. The private key stored in CanoKey cannot be read out. This reduced the chance of leakage of the private key.

## Supported algorithm

* RSA2048
* RSA4096\*
* X25519
* Ed25519
* NIST P-256 (secp256r1)
* NIST P-384 (secp384r1)
* secp256k1

\* Due to computing performance with STM32, RSA4096 cannot be generated in the card. However you can generate the key pair and import it to CanoKey.

Note that RSA3072 is not supported currently.

It is worth mentioning that one may refer to [this blog on multiple ecc curves](https://jia.je/crypto/2020/05/21/ecc-curves/) on aliases of one curve.

## Default values

One can learn from the [source code](https://github.com/canokeys/canokey-core/blob/master/applets/openpgp/openpgp.c) that

* PIN: default 123456, min len 6, max len 64
* Admin PIN: default 12345678, min len 8, max len 64
* Reset Code: default not set, min len 8, max len 64
* Signature PIN : forced // namely verify pin every signature
* Touch Policy: NO for SIG, DEC, AUT

## Touch Policy

There are three key slots for OpenPGP, namely Signature key(SIG), Encryption key(DEC) and Authentication key(AUT). For each key slot, there is a touch policy implemented by a `uint8`.

If this `uint8` is not 0, a successful `last_touch` is cached for this `uint8` seconds. So one may cache one touch for at most 255 seconds. One may implement "Always Touch/Required" policy by setting this `uint8` to 1.

To enable touch policy, one may go to [webconsole](https://console.canokeys.org/) admin applet, and find the touch policy there. Due to lack of development, this applet can only implement "Always Touch/Required" policy for each slot. If you want to enable cached policy, you may use APDU console provided by webconsole: first log into the admin applet, then switch to APDU console and send "00 09 XX YY" where XX ranges in 00-02 and YY ranges in 00-FF. For example, "00 09 00 FF" sets touch policy for SIG to be caching for 255 seconds. See [Admin Applet Deveplopment Guide](https://docs.canokeys.org/development/protocols/admin/) for more details on this command.

Note that even if there are three policies, touch result is cached in one variable(`last_touch`). So if you have one successful touch for SIG, when you want to use AUT within the policy time, touch is not needed.

One may disable Signature PIN (forcesig) after one enables touch policy for convenience with little loss of security.

## Using GnuPG

Please refer to the [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html) at the moment.

One may also refer to [https://github.com/drduh/YubiKey-Guide](https://github.com/drduh/YubiKey-Guide).

### Linux

Note that we recommend using ccid and pcsclite for gpg-agent/scdaemon to access CanoKey, namely in `~/.gnupg/scdaemon.conf`

```
pcsc-driver /usr/lib/libpcsclite.so
card-timeout 5
disable-ccid
```

You should setup ccid as in [setup](https://docs.canokeys.org/userguide/setup/).

## Debug and Report

### Linux

One may use `pcsc_scan` to check whether the smart card is detected by `pcscd`. Note that `pcscd` only uses the first smart card it detects, hence if you have other smart card readers in your box, you should disable them first.

One can use `pcscd -a -d -f` to monitor the status of card reader and the communication. One may start it after one kills the background instance. The log of it may be reported for troubleshooting.
