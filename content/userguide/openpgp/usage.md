+++
title = "Common Operations"
date = 2020-07-04T16:19:06+08:00
weight = 2
+++

For general OpenPGP and GnuPG usage, please refer to the [GNU Privacy Handbook](https://gnupg.org/gph/en/manual.html). This page summarizes the most common card operations with `gpg`.

## Inspecting the Card

With CanoKey connected, run:

```bash
gpg --card-status
```

This shows card information such as the serial number, the keys stored in the SIG / DEC / AUT slots, and the current retry counters.

## Administering the Card

Enter the interactive card administration mode:

```bash
gpg --card-edit
```

Inside the card edit prompt:

- Type `admin` to enable administrative commands (the Admin PIN is required for most of them).
- Type `generate` to generate a new key pair on the card. Firmware version 2.0.0 and later support generating RSA3072 / RSA4096 keys on the card; on earlier firmware, generate these keys on the computer and import them instead.
- Type `passwd` to change the PIN, Admin PIN, or Reset Code.

## Generating Keys on the Card

1. Run `gpg --card-edit`.
2. Type `admin`, then `generate`.
3. Follow the prompts to choose the key type and, optionally, create an off-card backup of the encryption key.

The keys are generated inside CanoKey and never leave the card in plain form.

## Importing Existing Keys

If you already have a GnuPG key pair, you can move its subkeys onto the card with `keytocard`:

1. Run `gpg --edit-key <KEYID>` with the private key available on the computer.
2. Select a subkey with `key <N>` (for example `key 1`), then type `keytocard` and choose the target slot (signature, encryption, or authentication).
3. Save with `save`.

{{% notice warning %}}
`keytocard` moves the private key to the card; the local copy is replaced by a stub pointing to the card. Make a backup of the private key beforehand if you want to keep an off-card copy.
{{% /notice %}}

## Locked Out of the Card

If the retry counters are exhausted and you have forgotten the Admin PIN, the OpenPGP applet can be reset from the CanoKey Console. This restores the factory defaults listed in [PIN and Touch Policies](pin-and-touch-policies/) and removes all keys stored in the OpenPGP applet.
