# CanoKeys 使用文档

使用 CanoKeys 提供的 WebAuthn 和其他技术保护您的账户。

## 目录

- [使用指南](userguide/)
- [开发指南](development/)

## 产品和硬件版本

截至 2022 年 9 月，CanoKey 项目下共有两款产品和两款开发者参考硬件。他们使用了同样的核心库 - [canokey-core](https://github.com/canokeys/canokey-core).

* **CanoKey Pigeon**: 这是当前在售版本。该版本具有塑料外壳。
* **CanoKey Epoxy**: 中文名 CanoKey 滴胶版。在早鸟（early bird）批次进行过发售。该版本外观为透明滴胶材质。
* **CanoKey STM32**: 该版本为开发者参考硬件。我们不销售该硬件版本。它使用了对开源项目更友好的 STM32 微控制器，使其对开发者更加便捷。该版本的硬件设计是开源的，可以[点击这里](https://github.com/canokeys/canokey-stm32)获得。该版本主要用于开发目的。
* **CanoKey nRF52**: 鉴于有些爱好者想要体验 CanoKey 开源固件，但无法自己制作 CanoKey STM32 版所用的硬件，我们推出了 CanoKey nRF52。它采用了 nRF52840 芯片，并且基于此芯片的各类 USB 设备非常容易买到。我们不销售该版本。该版本的开源固件可以[点击这里](https://github.com/canokeys/canokey-nrf52)获取。该版本主要用于开发目的。

为了使涉及密码学的运算更高效，发售版本选用了与开发者参考硬件（CanoKey STM32, CanoKey nRF52）**不同**的微控制器 (MCU)。此举有效提高了用户进行加密/解密/签名等操作的速度。

## 贡献方法

欢迎一起编辑本文档。请 fork [文档](https://github.com/canokeys/canokey-documentation) 然后发送 Pull Request 即可。
