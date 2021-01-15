+++
title = "FIDO2/U2F"
date =  2021-01-16T01:30:15+08:00
weight = 5
+++

## Supports

The implementations are following [CTAP2](https://fidoalliance.org/specs/fido-v2.0-ps-20190130/fido-client-to-authenticator-protocol-v2.0-ps-20190130.html) and [CTAP1/U2F](https://fidoalliance.org/specs/fido-u2f-v1.0-ps-20141009/fido-u2f-hid-protocol-ps-20141009.html) specifications.

Supported features:

- Up to 64 resident keys
- The HMAC extension

Currently only ECDSA ([COSE ES256](https://www.iana.org/assignments/cose/cose.xhtml#algorithms)) is supported.

## Multi-Factor Authentication

Use it after SETUP(refer to another user guide).

## OpenSSH

You may use the following command to generate a private key for ssh. Note that the "private key" stored in disk is not the real private key, see [here](https://undeadly.org/cgi?action=article;sid=20191115064850) for more info.

```
ssh-keygen -t ecdsa-sk
```

Note that `ed25519-sk` is not supported currently.

## PAM

Use `pam_u2f` provided by Yubico. One common scenerio is `sudo`.

## Management

Currently fine-grained management of the resident keys are not available.

You can reset the whole applet (clear all the resident keys) in Admin applet.

## HMAC-secret extension

One possible application of this extension is this tool [khefin](https://github.com/mjec/khefin).

