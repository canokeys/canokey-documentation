---
title: "Admin Applet"
date: 2019-11-28T10:18:29-05:00
weight: 30
---

To manage your CanoKey, you can use the admin applet to

- Reset OpenPGP / PIV / OATH / CTAP / NDEF / Pass.
- Import FIDO private key and certification.
- Configure the LED, NDEF, WebUSB and the enabled state of applets.
- Configure touch output (Pass) and the keyboard layout.
- Read firmware version, serial number and storage usage.

### 1. General Definitions

#### AID

The AID of the admin applet is `F000000000`.

#### CLA

CLA must be `00h`; the only exception is the chained blocks of Write FIDO Cert, which use `10h` (see below). Other CLA values return `6E00`.

#### Instructions

Instructions marked as Require PIN require a successful Verify PIN command to be performed before they are available; otherwise they return `6982`. Instruction codes not listed here return `6D00`.

| Name                   | Code | Require PIN                |
| ---------------------- | ---- | -------------------------- |
| Write FIDO Key         | 01h  | Y                          |
| Write FIDO Cert        | 02h  | Y                          |
| Reset OpenPGP          | 03h  | Y                          |
| Reset PIV              | 04h  | Y                          |
| Reset OATH             | 05h  | Y                          |
| Reset NDEF             | 07h  | Y                          |
| Set NDEF Read-only     | 08h  | Y                          |
| Reset CTAP             | 09h  | Y                          |
| Read CTAP SM2 Config   | 11h  | Y                          |
| Write CTAP SM2 Config  | 12h  | Y                          |
| Reset Pass             | 13h  | Y                          |
| NFC Enable             | 14h  | Read: N; Set: Y            |
| Verify PIN             | 20h  | N                          |
| Change PIN             | 21h  | Y                          |
| Write SN               | 30h  | Y                          |
| Get Version            | 31h  | N                          |
| Get SN                 | 32h  | N                          |
| Config                 | 40h  | Y                          |
| Flash Usage            | 41h  | N                          |
| Read Config            | 42h  | N                          |
| Read Pass Config       | 43h  | Y                          |
| Write Pass Config      | 44h  | Y                          |
| Write KBD Keymap       | 45h  | Y                          |
| Read KBD Keymap        | 46h  | Y                          |
| Clear KBD Keymap       | 47h  | Y                          |
| Factory Reset          | 50h  | N                          |
| Select                 | A4h  | N                          |
| Vendor Specific        | FFh  | Y                          |

### 2. Select

Selects the application for use.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | A4h   |
| P1    | 04h   |
| P2    | 00h   |
| Lc    | Length of AID (5) |
| Data  | AID (F0 00 00 00 00) |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 3. Verify PIN

Verify the PIN of this admin applet. The default PIN is `123456` (in string) or `31 32 33 34 35 36` (in hex).

{{% notice note %}}
PINs are independent between Admin / OpenPGP / PIV applets.
{{% /notice %}}

{{% notice warning %}}
The max retries is 3. When you exceed this limit, the applet will be locked. Successful verification will reset this limit.
{{% /notice %}}

If the input is empty (Lc = 0), the actual access status of the PIN is returned. If the PIN is verified, the applet answers with normal status bytes (SW = 9000). If the PIN is not checked and the verification is required, the applet answers with the status bytes 63CX, where 'X' encodes the number of further allowed retries.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 20h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of PIN or 0 |
| Data  | PIN |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 63CX | Verification failed, X retries left |
| 6700 | Incorrect length |
| 6983 | Applet is blocked |

### 4. Change PIN

After a successful verification, you can use this command to change your PIN **directly**. The length of the PIN should be between 6 and 64.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 21h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of new PIN |
| Data  | New PIN |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |

### 5. Write FIDO Key

You can manually write it using this command. The private key should be a secp256r1 (NIST P-256) key, 32 bytes long.

{{% notice note %}}
When the private key is updated, the certification should be also updated accordingly. Writing the key also resets the CTAP SM2 configuration to its default value.
{{% /notice %}}

{{% notice warning %}}
Once you write a new private key, your old 2FA credentials (FIDO2) will be **invalid**.
{{% /notice %}}

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 01h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of the key (20h) |
| Data  | Private key |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |

### 6. Write FIDO Certification

The FIDO certification is an X.509 der format certificate corresponding to your private key.

{{% notice note %}}
The maximum length of the certification is 1152 bytes. Since it may not fit in a single short APDU, use **ISO 7816-4 command chaining** to write it in blocks: all blocks except the last one use CLA `10h`, and the last block uses CLA `00h`. If the total length exceeds 1152 bytes, the write fails with `6700`.
{{% /notice %}}

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h (last block) or 10h (chained block) |
| INS   | 02h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of this certification block |
| Data  | Certification data block |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |

### 7. Reset OpenPGP / PIV / OATH / CTAP / NDEF / Pass

Executing these commands will reset the corresponding applets.

| Instruction Code | Applet  |
| ---------------- | ------- |
| 03h              | OpenPGP |
| 04h              | PIV     |
| 05h              | OATH    |
| 09h              | CTAP    |
| 07h              | NDEF    |
| 13h              | Pass    |

Resetting CTAP invalidates all FIDO2 credentials. NDEF and Pass are only available when the corresponding applet is compiled in; otherwise the command returns `6D00`.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 03h / 04h / 05h / 09h / 07h / 13h |
| P1    | 00h   |
| P2    | 00h   |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 8. Set NDEF read-only

Set if NDEF is read-only.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 08h   |
| P1    | 00h for read/write, 01h for read-only |
| P2    | 00h   |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 9. Read / Write CTAP SM2 Config

Read or write the SM2 configuration of the CTAP applet. The configuration is 8 bytes: a 32-bit `curve_id` followed by a 32-bit `algo_id` (COSE identifiers). When writing, `algo_id` must not conflict with the algorithm identifiers of ES256 / EdDSA / ML-DSA-65, otherwise `6A80` is returned.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 11h (read) / 12h (write) |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Write only: 08h |
| Data  | Write only: configuration data |

#### Response

Reading returns the 8-byte configuration data.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |
| 6A80 | Incorrect data |

### 10. NFC Enable

Read or set whether NFC is enabled. This command is implemented by the hardware platform. Setting requires a verified PIN.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 14h   |
| P1    | 00h to read, 01h to set |
| P2    | For set: 00h to disable, 01h to enable |
| Lc    | 0     |

#### Response

Reading returns 1 byte: 00h for disabled, 01h for enabled.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6982 | PIN not verified when setting |

### 11. Write SN

The SN can be only set **once**. Due to the limitation of OpenPGP card spec, the serial number is 4-byte long.

{{% notice note %}}
If you build your own CanoKey, you should use this command to write the SN. Otherwise, the SN has been already set.
{{% /notice %}}

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 30h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of the SN (4) |
| Data  | SN    |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |
| 6985 | SN has been set |

### 12. Get version

Read the version of the firmware, the hardware variant, or the canokey-core commit hash.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 31h   |
| P1    | 00h for firmware version, 01h for hardware variant, 02h for canokey-core commit hash |
| P2    | 00h   |
| Le    | 00h   |

#### Response

A string encoded in UTF-8.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 13. Get serial number

Read the SN of the CanoKey or the chip ID.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 32h   |
| P1    | 00h for CanoKey SN (4 bytes), 01h for chip ID |
| P2    | 00h   |
| Le    | 00h   |

#### Response

The raw data.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 14. Config

Configure the LED, NDEF, WebUSB and the enabled state of applets:

- The LED can be configured ON or OFF when not blinking. **The default value is ON.**
- NDEF, the WebUSB landing page and all feature switches are **ON by default.**

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 40h   |
| P1    | 01h: LED; 04h: NDEF; 05h: WebUSB landing page; 06h: feature switches |
| P2    | For P1 = 01h/04h/05h: 00h to disable, 01h to enable. For P1 = 06h: feature bitmask (see below) |

When P1 is 06h, each bit of P2 controls a feature (1 = enabled), and Lc must be 0:

| Bit | Feature       |
| --- | ------------- |
| 0   | Pass          |
| 1   | OpenPGP CCID  |
| 2   | OpenPGP NFC   |
| 3   | PIV CCID      |
| 4   | PIV NFC       |
| 5   | WebAuthn      |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6A86 | Incorrect P1/P2 |

### 15. Get flash usage

Get the flash usage. No PIN verification is required.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 41h   |
| P1    | 00h for total usage, 01h for per-applet usage |
| P2    | 00h   |
| Le    | At least 2 for P1 = 00h; at least 48 for P1 = 01h |

#### Response

For P1 = 00h, two bytes are returned: the first byte is the used space in KiB, and the second is the total size of the flash in KiB.

For P1 = 01h, 8 records of 6 bytes each are returned: `applet ID (1 byte) || flags (1 byte) || logical bytes (4 bytes, big-endian)`. The applet IDs are:

| ID  | Applet  |
| --- | ------- |
| 00h | System (file system overhead not attributable to applets) |
| 01h | Admin   |
| 02h | OpenPGP |
| 03h | PIV     |
| 04h | OATH    |
| 05h | CTAP    |
| 06h | NDEF    |
| 07h | Pass    |

Bit 0 of the flags is set when some files or attributes of the applet are absent (counted as zero), for example right after the applet is reset.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |

### 16. Get current configurations

Get current configurations. No PIN verification is required.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 42h   |
| P1    | 00h   |
| P2    | 00h   |
| Le    | At least 6 |

#### Response

6 bytes in total.

| Byte | Meaning        |
| ---- | -------------- |
| 1    | LED            |
| 2    | Reserved       |
| 3    | NDEF read-only |
| 4    | NDEF enabled   |
| 5    | WebUSB landing page enabled |
| 6    | Feature bitmask (same as Config) |

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 17. Read / Write Pass Config

Read or configure the touch output (Pass) applet. Pass has two slots: short touch and long touch. OATH slots are set by the OATH applet and cannot be written through Write Pass Config.

Read Pass Config (43h) returns the configuration of the two slots, short touch first, then long touch. The first byte of each slot is its type:

| Type | Meaning    | Following bytes                              |
| ---- | ---------- | -------------------------------------------- |
| 00h  | Off        | None                                         |
| 01h  | OATH       | Name length, name, with-enter flag           |
| 02h  | Static     | With-enter flag (the password is not returned) |
| 03h  | HMAC-SHA1  | None (the key is not returned)               |

The request of Write Pass Config (44h):

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 44h   |
| P1    | 01h for the short-touch slot, 02h for the long-touch slot |
| P2    | 00h   |
| Data  | Slot data (see below) |

The first byte of Data is the type:

- `00h`: disable the slot, Lc = 1.
- `02h`: static password, formatted as `02h || password length || password || with-enter flag`. The password is at most 32 bytes.
- `03h`: HMAC-SHA1, formatted as `03h || 14h || 20-byte key`.

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |
| 6A80 | Incorrect data |
| 6A86 | Incorrect P1/P2 |

### 18. KBD Keymap

Manage the keyboard layout used for keyboard output. The layout is a fixed 128-entry table; entry N maps ASCII code N to two bytes `{modifier, HID usage}`. A usage of 0 means the character is skipped. Once written, the stored table replaces the built-in QWERTY layout.

#### Write KBD Keymap (45h)

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 45h   |
| P1    | 00h   |
| P2    | Layout ID (host-defined, used to identify the layout) |
| Lc    | Length of the keymap (256) |
| Data  | 128 consecutive `{modifier, HID usage}` entries |

#### Read KBD Keymap (46h)

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 46h   |
| P1    | 00h   |
| P2    | 00h to read the layout ID (1 byte returned); 01h to read the keymap (256 bytes returned) |

`6A88` is returned if no keymap has been written.

#### Clear KBD Keymap (47h)

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 47h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 0     |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |
| 6A86 | Incorrect P1/P2 |
| 6A88 | Keymap not found |

### 19. Factory Reset

Reset all the applets (including the PIN and configuration of the admin applet itself; the SN is not reset). All FIDO2 credentials will be invalidated.
PIN retries must be used up for reset to begin, and the command is not available over NFC.
Once the command is executed, you must **touch within 2 seconds when blinking** until it responds with `9000`.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 50h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | 05h   |
| Data  | RESET (in ASCII) |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6982 | Not touched when blinking |
| 6985 | PIN not locked yet, or accessed over NFC |
| 6A80 | Incorrect data |

### 20. Vendor specific

This command isThis command is defined by the hardware platform (e.g. entering the firmware update mode). It requires a verified PIN and should not be used directly.
