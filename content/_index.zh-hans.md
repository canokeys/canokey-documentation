# CanoKey 使用文档

## 目录

- [使用指南](userguide/)
- [开发指南](development/)

## 产品和硬件版本

截至 2024 年 9 月，CanoKey 项目下共有三款产品和两款开发者参考硬件。他们使用了同样的核心库 - [canokey-core](https://github.com/canokeys/canokey-core)。

* **CanoKey Canary**: 这是即将发售的版本。该版本使用 USB Type C 接口。
* **CanoKey Pigeon**: 这是当前在售版本。该版本使用 USB Type A 接口。
* **CanoKey Epoxy**: 该版本已停售。该版本外观为透明滴胶材质。
* **CanoKey STM32**: 该版本从未销售过，仅供开发测试使用。该版本的硬件设计是开源的，可以[点击这里](https://github.com/canokeys/canokey-stm32)获得。**请注意，该版本不能提供任何安全保证，任何接触到设备的人可以获取明文密钥。**
* **CanoKey nRF52**: 该版本从未销售过，仅供开发测试使用。该版本采用 nRF52840 芯片，基于此芯片的各类 USB 设备非常容易买到。该版本的开源固件可以[点击这里](https://github.com/canokeys/canokey-nrf52)获取。**请注意，该版本不能提供任何安全保证，任何接触到设备的人可以获取明文密钥。**

零售版本使用 HED CIU98320B 芯片作为 MCU，该芯片通过了 EAL4+ 认证。**非零售版本不能提供任何安全保证，任何接触到设备的人可以获取明文密钥，仅供开发测试使用。**

## 贡献方法

欢迎一起编辑本文档。请 fork [文档](https://github.com/canokeys/canokey-documentation) 然后发送 Pull Request 即可。
