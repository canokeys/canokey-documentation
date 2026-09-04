+++
title = "Attestation"
date = 2020-07-11T22:33:15+08:00
weight = 6
+++

Firmware version 3.1.1 and later support PIV attestation.

Attestation is a way to prove that a key was generated on the device and never existed elsewhere. When asked, the device issues an attestation statement — an X.509 certificate for the public key of the slot in question, signed by a dedicated attestation key. Because only keys generated on the device can be attested, the attestation certificate is evidence that the private key was created inside the device and cannot have been imported. Imported keys are rejected by the attestation command.

Attestation is available for keys in slots 9A, 9C, 9D, 9E, and the retired slots 82–95.

## The Attestation Key

Attestation statements are signed by the key in slot F9, a non-standard slot that accepts only a P-256 key. The certificate of the attestation key is stored in data object `5FFF01`.

A PIV application reset preserves both the attestation key in slot F9 and the attestation certificate in `5FFF01`.

## Requesting an Attestation

The Attest command (`INS F9`) takes a slot number and returns a DER-encoded X.509 certificate for the key in that slot, signed by the attestation key. The command requires neither PIN verification nor management-key authentication.
