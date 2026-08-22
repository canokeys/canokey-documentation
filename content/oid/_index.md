+++
title = "Object Identifiers"
date = 2026-08-22T00:00:00+08:00
weight = 30
chapter = true
pre = "<b>3. </b>"
+++

### Chapter 3

# Object Identifiers

CanoKey has been assigned Private Enterprise Number (PEN) **66602** by IANA. All CanoKey-defined object identifiers are rooted at:

```
iso(1) identified-organization(3) dod(6) internet(1) private(4) enterprise(1) canokey(66602)
```

i.e. `1.3.6.1.4.1.66602`. This chapter lists the OID allocations used by CanoKey firmware and protocols.

## PIV Attestation

| OID | Description |
|:----|:------------|
| `1.3.6.1.4.1.66602.1` | PIV attestation |
| `1.3.6.1.4.1.66602.1.1` | Device serial number |
| `1.3.6.1.4.1.66602.1.2` | PIN and touch policies |

The two leaf OIDs appear as non-critical X.509 extensions in PIV attestation certificates (see [PIV Applet]({{< relref "development/protocols/piv.md" >}}), section 5.4):

- `1.3.6.1.4.1.66602.1.1` carries an OCTET STRING wrapping the four-byte device serial number, the same value returned by the PIV Get Serial command.
- `1.3.6.1.4.1.66602.1.2` carries an OCTET STRING wrapping two bytes: the PIN policy of the attested key followed by its touch policy.
