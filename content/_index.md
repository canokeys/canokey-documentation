# Documentation for CanoKey

Use the most advanced WebAuthn and other technologies to protect your accounts. Online and offline.

## Contents

- [User Guide](userguide/)
- [Development](development/)

## Products and hardware version

As of Auguest 2022, two commercial products and two developer hardware are available from CanoKey project. They uses the same core, which is [canokey-core](https://github.com/canokeys/canokey-core).

* **CanoKey Pigeon**: This is the commercial version currently on sale. It has a plastic shell.
* **CanoKey Epoxy**: This is the early-bird commercial version. It is wrapped with transparent epoxy.
* **CanoKey STM32**: This is the reference developer hardware. We don't sell this as a product. It uses an open source friendly MCU which is easier for developers. The hardware design is open source, available [here](https://github.com/canokeys/canokey-stm32). This version is mainly used for developing purpose.
* **CanoKey nRF52**: This version is developed for the reason that some of the hobbyists want to play with open source version but does not want to made the hardware on his or her own. It uses nRF52840 chip, of which there are lots of ready-made USB dongles on the market. We don't sell this as a product. The firmware design is open source, available [here](https://github.com/canokeys/canokey-nrf52). This version is mainly used for developing purpose.

In order to make crypto operations faster, the commercial versions **use different MCU** than the developer hardware (CanoKey STM32 and CanoKey nRF52). In such way, users benefit from faster encrypting/decrypting/signing operations when use commercial versions.

## Contribute to this documentation

Feel free to update this content. Just fork [this repo](https://github.com/canokeys/canokey-documentation) and send pull request to it.
