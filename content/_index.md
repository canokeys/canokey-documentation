# CanoKey User Documentation

## Contents

- [User Guide](userguide/)
- [Development Guide](development/)

## Product and Hardware Versions

As of September 2022, the CanoKey project offers three products and two developer reference hardware. They use the same core library - [canokey-core](https://github.com/canokeys/canokey-core).

* **CanoKey Canary**: This is the upcoming version. It uses a USB Type C interface.
* **CanoKey Pigeon**: This is the currently available version. It uses a USB Type A interface.
* **CanoKey Epoxy**: This version is no longer available. Its appearance is made of transparent epoxy.
* **CanoKey STM32**: This version has never been sold and is only for development testing. The hardware design of this version is open-source and can be obtained [here](https://github.com/canokeys/canokey-stm32). **Please note that this version cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys.**
* **CanoKey nRF52**: This version has never been sold and is only for development testing. It uses the nRF52840 chip, and various USB devices based on this chip are easily available. The open-source firmware for this version can be obtained [here](https://github.com/canokeys/canokey-nrf52). **Please note that this version cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys.**

The retail versions use the HED CIU98320B chip as the MCU, which has passed EAL4+ certification. **Non-retail versions cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys, and are only for development testing.**

## Contribution Method

We welcome contributions to this document. Please fork the [documentation](https://github.com/canokeys/canokey-documentation) and send a Pull Request.