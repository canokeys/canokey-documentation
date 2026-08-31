+++
title = "Credentials"
date = 2021-01-16T01:30:15+08:00
weight = 2
+++

This page describes how CanoKey stores and manages WebAuthn credentials, and the credential-related extensions it supports.

## Discoverable Credentials (Resident Keys)

CanoKey supports Discoverable Credentials, also known as Resident Keys.

CanoKey can store up to 64 Discoverable Credentials.

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
