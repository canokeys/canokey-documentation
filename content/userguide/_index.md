+++
title = "User Guide"
date = 2020-07-02T23:06:11+08:00
weight = 10
chapter = true
pre = "<b>1. </b>"
+++

# Chapter 1

## User Guide

Quick Start Guide

## Default Passwords

During the use of CanoKey, users will encounter various PINs. This page lists all default passwords and descriptions, aiming to help users distinguish different PINs.

| PIN Name           | Default Value | Description |
| :----------------- | :-----------: | :---------- |
| Admin PIN          | 123456        | Used to manage different applications on CanoKey, such as resetting applications, modifying NDEF configurations, etc. |
| FIDO2 PIN          | No default value | Some FIDO2 applications that enforce PIN usage will ask for this password. FIDO2 PIN has no preset value. Users will be prompted to set the FIDO2 PIN when they first use a FIDO2 application that forces PIN usage, at which point the user can set this PIN themselves. |
| OpenPGP PIN        | 123456        | Used for regular OpenPGP operations, such as OpenPGP signing, etc. |
| OpenPGP Admin PIN  | 12345678      | Used for management operations of the OpenPGP application. For example, it is required when generating an OpenPGP key pair on CanoKey or modifying OpenPGP key attributes. |
| PIV PIN            | 123456        | Used for regular PIV operations, such as PIV identity verification, signing through PKCS#11 call to PIV, etc. |
| PIV PUK            | 12345678      | Used to unlock the PIV PIN if the PIV PIN gets locked. |