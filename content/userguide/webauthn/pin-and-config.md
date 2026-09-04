+++
title = "PIN and Configuration"
date = 2021-01-16T01:30:15+08:00
weight = 1
+++

This page describes the PIN behavior of the WebAuthn application and the configuration options added in firmware version 3.1.1.

## PIN

By default, CanoKey does not set a PIN. Some websites and certain features (such as Discoverable Credentials management) require you to set a PIN. Please set it when prompted.

Firmware version 2.0.0 and later support PIN Protocol 2.

## Configuration Options

Firmware version 3.1.1 adds configuration options for the WebAuthn application, including:

- `alwaysUv`
- Minimum PIN length (`minPinLength`)
- Forced PIN change
- Long-press reset settings

These options are configured through the management software (such as the CanoKey Console).

{{% notice note %}}
On firmware version 3.1.1 and later, `alwaysUv` must be disabled to use U2F.
{{% /notice %}}
