# Documentation for CanoKey

Use the most advanced WebAuthn and other technologies to protect your accounts. Online and offline.

## Contents

- [User Guide](userguide/)
- [Development](development/)

## Products and hardware version

As of June 2022, two commercial products and a developer hardware is available from CanoKey project. They uses the same core, which is [canokey-core](https://github.com/canokeys/canokey-core).

* **CanoKey Pigeon**: This is the commercial version currently on sale. It has a plastic shell.
* **CanoKey Epoxy**: This is the early-bird commercial version. It is wrapped with transparent epoxy.
* **CanoKey STM32**: This is the reference developer hardware. We don't sell this as a product. It uses an open source friendly MCU which is easier for developers. The hardware design is open source, available [here](https://github.com/canokeys/canokey-stm32). This version is mainly used for developing purpose.

In order to make crypto operations faster, the commercial versions **use different MCU** than the developer hardware (CanoKey STM32). In such way, users benefit from faster encrypting/decrypting/signing operations when use commercial versions.

## Contribute to this documentation

Feel free to update this content. Just fork [this repo](https://github.com/canokeys/canokey-documentation) and send pull request to it.
