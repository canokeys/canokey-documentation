+++
title = "Credentials"
date = 2021-01-16T01:30:15+08:00
weight = 2
+++

This page describes how CanoKey stores and manages WebAuthn credentials, and the credential-related extensions it supports.

## Discoverable Credentials (Resident Keys)

CanoKey supports Discoverable Credentials, also known as Resident Keys.

On firmware versions earlier than 3.1.1, the number of Discoverable Credentials is limited to a fixed maximum of 64. On firmware version 3.1.1 and later, there is no fixed limit of 64: the actual capacity depends on available device storage, and the device also reports the remaining capacity.

## Credential Management

Firmware version 2.0.0 and later support Discoverable Credentials management.

{{% notice note %}}
Credential management requires a PIN to be set.
{{% /notice %}}

## Credential-Related Extensions

| Extension | Firmware Requirement |
| --- | --- |
| HMAC extensions (`hmac-secret`) | All versions |
| `credProtect` | Firmware 2.0.0 and later |
| `credBlob` | Firmware 2.0.0 and later |
| `largeBlobKey` | Firmware 2.0.0 and later |
| Large Blob | Firmware 2.0.0 and later |
| `minPinLength` | Firmware 3.1.1 and later |
| `thirdPartyPayment` | Firmware 3.1.1 and later |
| `hmac-secret-mc` (HMAC-secret during credential creation) | Firmware 3.1.1 and later |
