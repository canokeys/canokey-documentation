# CanoKey User Documentation

## Contents

- [User Guide](userguide/)
- [Development Guide](development/)

## Product and Hardware Versions

CanoKey has three products and two developer reference hardware platforms. They use the same [canokey-core](https://github.com/canokeys/canokey-core) codebase, but the features available may differ by firmware version.

* **CanoKey Canary**: This is the model currently available for sale. It uses a USB Type-C connector.
* **CanoKey Pigeon**: This model has been discontinued. It uses a USB Type-A connector, and its final firmware version is 2.0.1.
* **CanoKey Epoxy**: This model is no longer available. It has a transparent epoxy enclosure.
* **CanoKey STM32**: This version has never been sold and is only for development testing. The hardware design of this version is open-source and can be obtained [here](https://github.com/canokeys/canokey-stm32). **Please note that this version cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys.**
* **CanoKey nRF52**: This version has never been sold and is only for development testing. It uses the nRF52840 chip, and various USB devices based on this chip are easily available. The open-source firmware for this version can be obtained [here](https://github.com/canokeys/canokey-nrf52). **Please note that this version cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys.**

The retail versions use the HED CIU98320B chip as the MCU, which has passed EAL4+ certification. **Non-retail versions cannot provide any security assurances; anyone with access to the device can obtain the plaintext keys, and are only for development testing.**

## Contribution Method

Contributions to this document are welcome. Please fork the [documentation](https://github.com/canokeys/canokey-documentation) and send a Pull Request.
