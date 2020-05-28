---
title: "Admin Applet"
date: 2019-11-28T10:18:29-05:00
weight: 30
---

To manage your CanoKey, you can use the admin applet to

- Reset OpenPGP / PIV / OATH.
- Import FIDO private key and certification.

### 1. General Definitions

#### AID

The AID of the admin applet is `F000000000`.

#### Instructions

Instructions marked as Require PIN require a successful Verify PIN command to be performed before they are available.

| Name            | Code | Require PIN |
| --------------- | ---- | ----------- |
| Write FIDO Key  | 01h  | Y           |
| Write FIDO Cert | 02h  | Y           |
| Reset OpenPGP   | 03h  | Y           |
| Reset PIV       | 04h  | Y           |
| Reset OATH      | 05h  | Y           |
| Verify PIN      | 20h  | N           |
| Change PIN      | 21h  | Y           |
| Write SN        | 30h  | Y           |
| Get Version     | 31h  | N           |
| Config          | 40h  | Y           |
| Flash Usage     | 41h  | Y           |
| Select          | A4h  | N           |
| Vendor Specific | FFh  | Y           |

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

The private key is shared by U2F and FIDO2, which is used to sign the attestation data in the registration phase. You can manually write it using this command. The private key should be a secp256r1 (NIST P-256) key.

{{% notice note %}}
When the private key is updated, the certification should be also updated accordingly.
{{% /notice %}}

{{% notice warning %}}
Once you write a new private key, your old 2FA credentials (U2F/FIDO2) will be **invalid**.
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

The FIDO certification is an X.509 der format certificate corresponding to your private key. Use an **extended command APDU** to set it.

{{% notice note %}}
The maximum length of the certification is 1152 bytes.
{{% /notice %}}

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 02h   |
| P1    | 00h   |
| P2    | 00h   |
| Lc    | Length of the certification (2 bytes) |
| Data  | The certification |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |
| 6700 | Incorrect length |

### 7. Reset OpenPGP / PIV / OATH

Executing these commands will reset the corresponding applets.

| Instruction Code | Applet  |
| ---------------- | ------- |
| 03h              | OpenPGP |
| 04h              | PIV     |
| 05h              | OATH    |

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 03h / 04h / 05h |
| P1    | 00h   |
| P2    | 00h   |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 8. Write SN

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

### 9. Get version

Read the version of the firmware.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 31h   |
| P1    | 00h   |
| P2    | 00h   |
| LE    | 00h   |

#### Response

The version encoded in UTF-8.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 10. Config

Configure the USB interfaces and the LED status:

- The LED can be configured ON or OFF when not blinking. **The default value is ON.**
- The keyboard interface can be enabled to input the HOTP by simply touching the key. **The default value is OFF.**

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 40h   |
| P1    | 01h: LED; 03h: Keyboard |
| P2    | 00h: Off, 01h: On |

#### Response

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 11. Get flash usage

Get the capacity of the flash.

#### Request

| Field | Value |
| ----- | ----- |
| CLA   | 00h   |
| INS   | 41h   |
| P1    | 00h   |
| P2    | 00h   |

#### Response

Two bytes in total. The first byte indicates the free space in KiB. And the second indicates the total size of the flash in KiB.

| SW   | Description |
| ---- | ----------- |
| 9000 | Success     |

### 12. Vendor specific

This command is reserved for development use and invalid in a release version.
