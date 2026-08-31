+++
title = "PIN and Touch Policies"
date = 2020-07-04T16:19:06+08:00
weight = 1
+++

The OpenPGP application on CanoKey uses a user PIN, an Admin PIN, and an optional Reset Code, and can additionally require a physical touch before cryptographic operations. This page describes the default values and the policy semantics.

## Default Values

| Item | Default | Minimum Length | Maximum Length |
| --- | --- | --- | --- |
| PIN | `123456` | 6 | 64 |
| Admin PIN | `12345678` | 8 | 64 |
| Reset Code | empty | 8 | 64 |
| Signature PIN | forced (PIN verification required for each signature) | — | — |
| Touch Policy (SIG, DEC, AUT) | off | — | — |
| Touch Cache Time | 0 | — | — |
| Retry counters (PIN, Reset Code, Admin PIN) | 3 | — | — |

## PIN Policy

For DEC and AUT keys, after the PIN verification is successful, verification will not be required again until CanoKey is disconnected and reinserted.

For SIG, if `forcesig` is on, a PIN is required for each signature; otherwise, a PIN is only required for the first signature after power-on.

## Touch Policy

{{% notice note %}}
Touch policy is only effective when using the USB interface.
{{% /notice %}}

Depending on the firmware version, you can set the touch policy for SIG, DEC, and AUT in the CanoKey Console or via the `gpg` command. The value of touch cache time ranges from 0 to 255 seconds (0 means no cache).

### Firmware Version < 1.4

Please use the "Settings" application in the CanoKey Console to modify the touch policy.

### Firmware Version >= 1.5.0

Please use GnuPG to modify the touch policy.
