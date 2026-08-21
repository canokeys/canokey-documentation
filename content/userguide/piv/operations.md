+++
title = "Common Operations"
date = 2020-07-11T22:33:15+08:00
weight = 9
+++

{{% notice note %}}
As PIV is typically issued by system administrators and used by regular users, please review the documentation to understand the following content before proceeding.
{{% /notice %}}

## Tools

It is recommended to use [yubico-piv-tool](https://developers.yubico.com/yubico-piv-tool/Releases/) for related operations.

## Importing Keys and Certificates Separately

If the key and certificate are in two separate files, they need to be imported separately.

Importing the private key:
```sh
yubico-piv-tool -r canokey -a import-key -s 9a -i private-key.pem
```

Importing the certificate:
```sh
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

Here, `-s 9a` indicates using the 9A key slot, which can be changed as needed.

## Importing PKCS#12 File

To import a PKCS#12 file (.p12 or .pfx) containing both the private key and certificate, execute:
```sh
yubico-piv-tool -r canokey -a import-key -a import-certificate -K PKCS12 -s 9a -i certificate.p12
```

## Generating Key and Self-signing

Generate a new private key and self-sign it:
```sh
yubico-piv-tool -r canokey -a generate -s 9a -A RSA2048 -o public-key.pem
yubico-piv-tool -r canokey -a verify-pin -a selfsign -s 9a -S "/CN=Test Certificate" -i public-key.pem -o certificate.pem
yubico-piv-tool -r canokey -a import-certificate -s 9a -i certificate.pem
```

## Additional Steps for Windows

Since Windows caches certificate information based on CHUID, you need to update the CHUID after certificate import on Windows:
```sh
yubico-piv-tool -r canokey -a set-chuid
```
