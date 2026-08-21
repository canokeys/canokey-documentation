+++
title = "Reset"
date = 2021-01-16T01:30:15+08:00
weight = 3
+++

Resetting the WebAuthn application erases the credentials stored on CanoKey.

## Resetting from the Console

As described in [Settings](../../admin/), you can reset each application individually through the console if you know the Admin PIN. The data of the reset application will be erased.

If you do not remember the Admin PIN, you can reset CanoKey when the Admin PIN is completely locked out. In the settings application of the console, click "Reset", and the LED indicator of CanoKey will flash. When it flashes, please touch the key; repeat the above operation until it stops flashing.

All user application data will be erased. Factory-provisioned data, such as the serial number and device attestation keys, is preserved.

## Resetting from Clients

The CTAP reset operation is also available from WebAuthn clients.
