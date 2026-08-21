+++
title = "Challenge-Response"
date = 2022-01-03T18:59:12+08:00
weight = 3
+++

Firmware version 3.1.1 and later provide two HMAC-SHA1 challenge-response slots, intended for applications such as [KeePassXC](https://keepassxc.org/) that use challenge-response to unlock a database.

A host can send a challenge of up to 64 bytes using OATH/PCSC commands, and the device returns the raw 20-byte HMAC-SHA1 response.

The keys stored in the challenge-response slots are write-only and cannot be read through the configuration interface.
