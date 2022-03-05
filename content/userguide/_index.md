+++
title = "User Guide"
date = 2020-07-02T23:06:11+08:00
weight = 10
chapter = true
pre = "<b>1. </b>"
+++

### Chapter 1

# User Guide

Quick start guides for users.

## Information about PINs

You might meet several PINs when using CanoKeys. Here is a list to help you distinguish different PIN.

| PIN Name | Default value | Description |
| :------- | :-----------: | :---------- |
| Admin PIN | 123456| The pin to manage each applet and access admin functions on Web Console. For example, reset individual applet, change touch policy. |
| FIDO2 PIN |  | The pin requested by PIN-enabled FIDO2/U2F application. The PIN is not set by default. When any of the websites require a PIN for your FIDO authentication, you are prompted to set your PIN yourself. |
| GPG PIN | 123456 | The PIN to do normal GPG actions, for example, GPG signing. |
| GPG Admin PIN | 12345678 | The PIN to manage your GPG applet. For example, generate a GPG keypair oncard, change default GPG key-attr. |
| PIV PIN | 123456 | The PIN to do normal PIV actions, for example, PIV authentication or signing using PKCS#11. |
| PIV PUK | 12345678 | The password to reset your PIN when your PIN has been locked. |

