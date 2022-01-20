+++
title = "PIV"
date =  2020-07-11T22:33:15+08:00
weight = 25
+++

Personal Identity Verification (PIV) is a US government standard defined as [FIPS 201](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.201-2.pdf). PIV uses a smartcard to store the key for signing / encryption. PIV is mainly used in non-web scenarios.

## Basic information
### Supported algorithm

* RSA2048
* NIST P-256
* NIST P-384

### Default values

* PIN: default 123456
* PUK: default 12345678
* Management Key (Known as `Security Officer PIN` or `SO pin` in pkcs11): default 010203040506070801020304050607080102030405060708

## User guide

The following user guide was written with `yubico-piv-tool` version 2.2.1 and `opensc` version 0.22.0 under Linux.

### Initial PIV on your card

Usually, before you start to use PIV of Canokey, you should initial the Cardholder Unique Identifier (CHUID) and Cardholder Capability Container (CCC)

```
yubico-piv-tool -r canokeys -a set-ccc
yubico-piv-tool -r canokeys -a set-chuid
```

### Generating keys and certificates

In this example, we are generating a key with NIST P-384 algorithm and name the public key as public384.pem.

```
$ yubico-piv-tool -r canokeys -s 9a -a generate -A ECCP384 -o public384.pem
Successfully generated a new private key.
```

Then generate the certificate for that key. We are taking SSH key as an example and exporting the certificate as cert.pem.

```
$ yubico-piv-tool -r canokeys -a verify-pin -a selfsign-certificate -s 9a -S "/CN=SSH key/" -i public384.pem -o certificate.pem
Enter PIN: 
Successfully verified PIN.
Successfully generated a new self signed certificate.
```

And now we can import the certificate.

```
$ yubico-piv-tool -r canokeys -a import-certificate -s 9a -i certificate.pem
Successfully imported a new certificate.
```

If you want to verify your key is there, you can do so by

```
$ yubico-piv-tool -r canokeys -a read-certificate  -s 9a
```

### Authentication with SSH

#### With ssh-agent

Run `ssh-agent` with the pkcs11 provider. Here we use OpenSC. In the following example, the OpenSC library is available in /usr/lib64/opensc-pkcs11.so.

```
$ eval $(ssh-agent -P /usr/lib64/opensc-pkcs11.so)
Agent pid 61757
```

And then add the key by

```
$ ssh-add -s /usr/lib64/opensc-pkcs11.so
Enter passphrase for PKCS#11: 
Card added: /usr/lib64/opensc-pkcs11.so
```

Now you can check your SSH public key.

```
$ ssh-add -L
ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBDydOf6U+9/hAknZnJckyFwoinXKVEjTZkVV7bKNDZs4XsaHUoQix3z3+LsVn9WsLKeAKtigv2GS/removed/Snip12345678901234567890123456789012/SnipSnip== PIV AUTH pubkey
```

Add the printed key to your remote machine by any of your preferred ways. Now you can ssh to your remote machine as usual.

#### Without ssh-agent

Without ssh-agent, you need to input the PIV pin each time you ssh to a machine.

To check your SSH public key

```
ssh-keygen -D /usr/lib64/opensc-pkcs11.so  -e
```

Then copy the pub key to your remote machine by any of your preferred ways. Now ssh to your remote machine by providing the PKCS#11 library

```
$ ssh -I /usr/lib64/opensc-pkcs11.so remote.lan
Warning: Permanently added 'remote.lan' (ED25519) to the list of known hosts.
Enter PIN for 'SSH key': 
X11 forwarding request failed on channel 0
Last login: Thu Jan 20 22:57:23 2022 from 192.168.1.246
[user@remote ~]$ 
```

## Resetting the PIV applet

If your PIN and PUK both have been blocked, you can use `yubico-piv-tool -r canokeys -a reset` to reset the PIV applet. After that, all the PIV data has been erased in your Canokey. And your PIN, PUK and management key are all back to default. **Remember to initial the PIV again after the reset**.
