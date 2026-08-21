---
title: "NDEF Applet"
date: 2026-08-21T00:00:00+08:00
weight: 15
---

The CanoKey NDEF applet implements an [NFC Forum Type-4 Tag](http://apps4android.org/nfc-specifications/NFCForum-TS-Type-4-Tag_2.0.pdf), exposing an NDEF message over the NFC interface. This page documents the file layout and the commands as implemented by CanoKey. Refer to the NFC Forum specification for the standard definitions.

## 1. AID and File Selection

The NDEF tag application identifier is:

```text
D2 76 00 00 85 01 01
```

Select it with `00 A4 04 00 07 D2760000850101`. Afterwards, select one of the two files with Select (`P1 = 00`, `P2 = 0C`) and the two-byte file identifier in the data field:

| File ID | File |
|:-------:|:-----|
| `E103` | Capability Container (CC) |
| `0001` | NDEF file |

### 1.1 Instructions

| INS | Name |
|:---:|:-----|
| `A4` | Select |
| `B0` | Read Binary |
| `D6` | Update Binary |

Firmware version 3.1.1 and later support ISO 7816-4 command chaining (`CLA = 10`) for Update Binary.

## 2. Capability Container

The Capability Container is 15 bytes long and read-only from the tag interface:

| Offset | Length | Field | Value |
|:------:|:------:|:------|:------|
| 0 | 2 | CCLEN | `000F` |
| 2 | 1 | Mapping version | `20` (version 2.0) |
| 3 | 2 | MLe | `0400` |
| 5 | 2 | MLc | `0400` |
| 7 | 1 | NDEF File Control TLV: tag | `04` |
| 8 | 1 | NDEF File Control TLV: length | `06` |
| 9 | 2 | NDEF file identifier | `0001` |
| 11 | 2 | Maximum NDEF file size | `0400` |
| 13 | 1 | NDEF file read access | `00` (read access granted) |
| 14 | 1 | NDEF file write access | `00` (write access granted) or `FF` (read-only) |

Update Binary on the Capability Container is rejected with `6985`; the write access byte can only be changed through the Admin applet (see Section 5).

## 3. NDEF File

The NDEF file holds up to 1024 bytes: a two-byte big-endian NLEN followed by the NDEF message, so the maximum NDEF message length is 1022 bytes.

The factory default content is a single URI record pointing to `https://canokeys.org`:

```text
00 11                                        ; NLEN = 17
D1 01 0D 55 04 63 61 6E 6F 6B 65 79 73 2E 6F 72 67
```

(`D1`: short record, type name format 1; type `55` = "U", URI; identifier `04` = the `https://` prefix; payload `canokeys.org`.)

## 4. Read Binary and Update Binary

Read Binary (`INS = B0`) takes the offset in `P1:P2` (big-endian) and the number of bytes to read in `Le`. Reads beyond the end of the selected file are rejected with `6700`. Issuing the command without a selected file is rejected with `6985`.

Update Binary (`INS = D6`) takes the offset in `P1:P2` and the data in the command data field; `offset + Lc` must not exceed the NDEF file size (1024 bytes). Writes are only accepted when the write access byte of the CC is `00`; otherwise the command fails with `6982`.

To replace the NDEF message, update the message bytes first and then the NLEN field at offset 0, as usual for Type-4 Tags.

## 5. Read-only Flag

The NDEF message can be made read-only through the Admin applet instruction `08h` (`P1 = 00` for read/write, `P1 = 01` for read-only), which rewrites the write access byte of the Capability Container; see the [Admin Applet documentation](../admin/). When read-only is set, Update Binary on the NDEF file fails with `6982`.

The Admin applet can also reset the NDEF applet (instruction `07h`), which restores the default Capability Container and the default `https://canokeys.org` content.

## 6. Status Codes

| SW | Meaning |
|:---|:--------|
| `9000` | Success |
| `6700` | Wrong length or offset |
| `6982` | Security status not satisfied (write access disabled) |
| `6985` | Conditions not satisfied (no file selected; CC not writable) |
| `6A82` | File not found (unknown file identifier) |
| `6A86` | Wrong P1 / P2 |
| `6D00` | Instruction not supported |
