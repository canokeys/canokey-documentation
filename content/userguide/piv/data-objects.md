+++
title = "Data Objects"
date = 2020-07-11T22:33:15+08:00
weight = 5
+++

Besides certificates, the PIV applet stores a number of standard data objects. The table below lists each object with its tag and capacity.

| Tag | Data object | Capacity | Read access |
|:----|:------------|---------:|:------------|
| `5FC102` | Card Holder Unique Identifier (CHUID) | 2916 bytes | Public |
| `5FC107` | Card Capability Container | 287 bytes | Public |
| `5FC109` | Printed Information | 245 bytes | PIN |
| `5FC106` | Security Object | 245 bytes | Public |
| `5FC103` | Cardholder Fingerprints | 512 bytes | PIN |
| `5FC108` | Cardholder Facial Image | 512 bytes | PIN |
| `5FC121` | Cardholder Iris Images | 512 bytes | PIN |
| `5FC10C` | Key History | 32 bytes | Public |
| `5FFF00` | Admin Data | 128 bytes | Public |

Reading Printed Information, Cardholder Fingerprints, Cardholder Facial Image, or Cardholder Iris Images requires PIN verification. Writing any of these objects requires management-key authentication. Optional objects consume storage only after data is written to them.

Certificate objects, including the Retired Key Management certificate objects supported from firmware version 3.1.1, are described in [Certificates](certificates/).
