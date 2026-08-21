+++
title = "PIN, PUK, and Management Key"
date = 2020-07-11T22:33:15+08:00
weight = 2
+++

The PIV applet is protected by three secrets: a PIN, a PUK, and a management key. Each of them guards a different class of operations.

## PIN

The PIN is the everyday user credential. Depending on the [PIN policy](pin-touch-policies/) of each slot, it must be verified before the private key in a slot can be used for signing, decryption, or key agreement. It also protects read access to certain data objects, such as Printed Information and the biometric objects. The default PIN is `123456`.

## PUK

The PUK (PIN Unblocking Key) exists to recover a blocked PIN. When the PIN has been entered incorrectly too many times and is blocked, the PUK is used with the Reset Retry Counter command to set a new PIN and restore its retry counter. The default PUK is `12345678`.

## Management Key

The management key is the administrator credential. It is a 24-byte symmetric key with the default value `010203040506070801020304050607080102030405060708`. On firmware version 3.1.1 and later it uses AES-192 (algorithm ID `0A`); earlier firmware uses Triple-DES. The management key itself can be changed with the Set Management Key command.

## Blocking and Unblocking

Entering the PIN incorrectly exhausts its retry counter, after which the PIN is blocked and operations that require it fail. A blocked PIN can be unblocked with the PUK. If the PUK is also blocked, the PIV application can be reset; the Reset command is accepted only when both the PIN and the PUK are blocked. A reset restores the PIV user data and defaults while preserving the attestation key in slot F9 and the attestation certificate in data object `5FFF01`; other writable data objects are cleared.

## Changing the Retry Counters

Firmware version 3.1.1 and later support setting the PIN and PUK retry counters to any value from 1 to 15. This operation requires both management-key authentication and PIN verification, and resets the PIN and PUK to their default values.

## Which Operations Require Which Secret

Operations that require management-key authentication:

* Generating a key pair in a slot
* Importing an asymmetric key
* Writing data objects, including certificates
* Changing the management key
* Moving a key between slots or deleting a key (firmware 3.1.1 and later)
* Setting the PIN and PUK retry counters (firmware 3.1.1 and later; PIN verification is additionally required)

Operations that require the PIN:

* Using a private key for signing, decryption, or key agreement, according to the slot's [PIN policy](pin-touch-policies/)
* Reading the PIN-protected data objects (Printed Information, Cardholder Fingerprints, Cardholder Facial Image, Cardholder Iris Images)
* Changing the PIN

Operations that require the PUK:

* Unblocking a blocked PIN (Reset Retry Counter)
