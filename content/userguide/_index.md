+++
title = "User Guide"
date = 2020-07-02T23:06:11+08:00
weight = 10
chapter = true
pre = "<b>1. </b>"
+++

# Chapter 1

## Glossary

- **FIDO**: Fast Identity Online, an open industry alliance dedicated to setting and promoting passwordless authentication standards.
- **U2F**: Universal 2nd Factor, a standard protocol of the FIDO alliance that provides two-factor authentication through external media like USB devices, enhancing account security. This protocol has been replaced by CTAP 2.0 / 2.1. CanoKey supports U2F and CTAP 2.0 / 2.1 protocols.
- **WebAuthn**: Web Authentication, an authentication protocol defined by W3C. Its device-side implementations include CTAP / U2F protocols.
- **Passkey**: A Passkey is an implementation of WebAuthn. CanoKey can store Passkey keys through the CTAP protocol.
- **OpenPGP**: Open Pretty Good Privacy, an open standard protocol used for encrypting and signing data, widely used for securing communications such as emails. CanoKey supports all mandatory features in OpenPGP 3.4.1.
- **GnuPG**: Gnu Privacy Guard, a free software tool implementing the OpenPGP standard, used for encrypting and signing data.
- **PIV**: Personal Identity Verification, a U.S. government identity authentication standard designed to enhance personal identity verification security. CanoKey supports all mandatory and some extended features of the PIV standard.
- **NDEF**: NFC Data Exchange Format, a standard format for exchanging data between NFC devices. CanoKey supports emulation of NFC tags compliant with the NDEF format.
- **OTP**: One-Time Password, a single-use password used for one-time authentication, enhancing security and often used in two-factor authentication. Common OTPs include HOTP and TOTP. CanoKey supports storing keys for both OTP types.
- **HOTP**: HMAC-based One-Time Password, a one-time password generated based on the HMAC (Hash-based Message Authentication Code) algorithm.
- **TOTP**: Time-based One-Time Password, a dynamic one-time password based on time, generated using the current time and a shared key.
- **WebUSB**: A protocol that allows web pages to directly communicate with USB devices, simplifying device connection and data transfer. CanoKey supports configuration via WebUSB.

## Default PINs

During the use of CanoKey, users will encounter various PINs. This page lists all default passwords and descriptions, aiming to help users distinguish different PINs.

| PIN Name           | Default Value | Description |
| :----------------- | :-----------: | :---------- |
| Admin PIN          | 123456        | Used to manage different applications on CanoKey, such as resetting applications, modifying NDEF configurations, etc. |
| FIDO2 PIN          | No default value | Some FIDO2 applications that enforce PIN usage will ask for this password. FIDO2 PIN has no preset value. Users will be prompted to set the FIDO2 PIN when they first use a FIDO2 application that forces PIN usage, at which point the user can set this PIN themselves. |
| OpenPGP PIN        | 123456        | Used for regular OpenPGP operations, such as OpenPGP signing, etc. |
| OpenPGP Admin PIN  | 12345678      | Used for management operations of the OpenPGP application. For example, it is required when generating an OpenPGP key pair on CanoKey or modifying OpenPGP key attributes. |
| PIV PIN            | 123456        | Used for regular PIV operations, such as PIV identity verification, signing through PKCS#11 call to PIV, etc. |
| PIV PUK            | 12345678      | Used to unlock the PIV PIN if the PIV PIN gets locked. |