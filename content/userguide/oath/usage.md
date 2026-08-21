+++
title = "Managing Credentials"
date = 2022-01-03T18:59:12+08:00
weight = 2
+++

Use CanoKey Console or `ckman` to manage OATH credentials.

## CanoKey Console

The web version of CanoKey Console requires Chrome or a Chromium-based browser.

1. Open the [OATH page](https://console.canokeys.org/oath) in CanoKey Console and connect the CanoKey.
2. Select `+` at the top of the page, then choose to scan a QR code with the camera, scan a QR code on the screen, or enter the credential manually.
3. For manual entry, provide the issuer, account, secret, type, algorithm, number of digits, and other required fields, then confirm the addition.

TOTP codes are displayed automatically. For a touch-protected TOTP credential, select the touch icon and then touch the CanoKey. To view an HOTP code, select the refresh icon beside the credential.

{{% notice warning %}}
The HOTP counter advances each time an HOTP code is calculated, so avoid calculating a code repeatedly.
{{% /notice %}}

## ckman

After installing [CanoKey Manager](https://github.com/canokeys/yubikey-manager), use `ckman` to manage OATH credentials. The following examples connect through a smart-card reader whose name contains `Canokeys`. If your system reports a different reader name, adjust the `--reader` argument accordingly.

### Importing an `otpauth://` URI

If the authentication service provides an `otpauth://` URI, import it directly:

```sh
ckman --reader "Canokeys" oath accounts uri "otpauth://totp/EXAMPLE.COM:username?secret=SOMESECRET&issuer=EXAMPLE.COM&algorithm=SHA1&digits=6&period=30"
```

### Adding a Credential with Parameters

You can also provide each credential parameter separately. The following command adds a TOTP credential that uses SHA-1, six digits, and a 30-second period:

```sh
ckman --reader "Canokeys" oath accounts add \
  --oath-type TOTP \
  --algorithm SHA1 \
  --digits 6 \
  --period 30 \
  --issuer "EXAMPLE.COM" \
  "username" "SOMESECRET"
```

Run `ckman oath accounts add --help` for HOTP, touch confirmation, and other available options.

### Listing Credentials

List the OATH credentials stored on the device:

```sh
ckman --reader "Canokeys" oath accounts list
```

### Calculating Codes

Calculate codes for all TOTP credentials:

```sh
ckman --reader "Canokeys" oath accounts code
```

You can also provide a name to calculate a specific credential:

```sh
ckman --reader "Canokeys" oath accounts code "EXAMPLE.COM:username"
```

### Deleting a Credential

```sh
ckman --reader "Canokeys" oath accounts delete "EXAMPLE.COM:username"
```
