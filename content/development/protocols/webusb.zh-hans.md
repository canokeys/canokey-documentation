---
title: "WebUSB"
date: 2019-11-28T10:18:29-05:00
weight: 50
---

### 1. 概述

CanoKey 支持 [WebUSB](https://wicg.github.io/webusb/) 协议，以便于管理。您可以使用我们开源的[控制台](http://)来管理您的 CanoKey，也可以通过 WebUSB API 开发自己的控制台。

#### 1.1 驱动

CanoKey 是一个 [WinUSB 设备](https://docs.microsoft.com/en-us/windows-hardware/drivers/usbcon/automatic-installation-of-winusb)，其固件定义了特定的 Microsoft 操作系统（OS）特征描述符，将兼容 ID 报告为 "WINUSB"。因此，在 Windows 上使用时无需安装额外的驱动。当然，在 Linux 或 macOS 上也不需要驱动。

#### 1.2 接口与管道

CanoKey 使用索引为 1 的接口，传输类型为控制传输（control transfer）。默认 EP 大小为 16 字节。

### 2. 消息

WebUSB 接口上的消息本质上是 APDU 命令。收发一对 APDU 命令需要两个阶段：

1. 发送命令 APDU
2. 获取响应 APDU

每种消息都是一个 vendor-specific 请求，定义如下：

| bRequest | 值    |
|----------|-------|
| CMD      | 00h   |
| RESP     | 01h   |
| STAT     | 02h   |

#### 2.1 命令 APDU

以下控制管道请求用于发送命令 APDU。

| bmRequestType | bRequest | wValue | wIndex | wLength        | Data  |
| ------------- | -------- | ------ | ------ | -------------- | ----- |
| 01000001B     | CMD      | 0000h  | 1      | 数据长度       | bytes |


#### 2.2 获取响应 APDU

以下控制管道请求用于获取响应 APDU。

| bmRequestType | bRequest | wValue | wIndex | wLength | Data |
| ------------- | -------- | ------ | ------ | ------- | ---- |
| 11000001B     | RESP     | 0000h  | 1      | 0       | N/A  |

设备发送的响应不超过 1500 字节。

#### 2.3 获取执行状态

以下控制管道请求用于获取卡片的状态。

| bmRequestType | bRequest | wValue | wIndex | wLength | Data |
| ------------- | -------- | ------ | ------ | ------- | ---- |
| 11000001B     | STAT     | 0000h  | 1      | 0       | N/A  |

响应数据长度为 1 字节：0x01 表示正在处理中，0x00 表示处理完成、可以使用 `RESP` 命令获取结果，其他值表示无效状态。

{{% notice note %}}
如果命令仍在处理中，响应将为空。
{{% /notice %}}

### 3. 示例代码

如果您有 CanoKey，现在就可以试一试。

#### 3.1 连接

点击下方按钮连接一个 CanoKey。

<button id="connect" class="btn btn-default">Connect</button>
<span id="device-info"></span>

{{%expand "显示代码"%}}
```js
let connect = document.getElementById('connect');
let info = document.getElementById('device-info');
connect.addEventListener('click', async () => {
  let device;
  try {
    device = await navigator.usb.requestDevice({ filters: [{
      classCode: 0xFF, // vendor-specific
    }]});
  } catch (err) {
    info.innerText = 'No device selected';
  }

  if (device !== undefined) {
    await device.open();
    await device.claimInterface(1);
    info.innerText = 'A CanoKey is selected';
  }
});
```
{{% /expand%}}

#### 3.2 收发

连接 CanoKey 后，在此处输入命令 APDU，例如 `00A4040005F000000000`，然后点击 "Send"。

<style>
input[disabled] {
color: rgb(84, 84, 84);
background-color: rgb(235, 235, 228);
}
</style>
<input class="form-element-input" type="text" id="capdu" placeholder="Command APDU" disabled>
<button id="send" class="btn btn-default">Send</button>
<p id="rapdu">Response: </p>

{{%expand "显示代码"%}}
```js
function byteToHexString(uint8arr) {
  if (!uint8arr) return '';
  var hexStr = '';
  for (var i = 0; i < uint8arr.length; i++) {
    var hex = (uint8arr[i] & 0xff).toString(16);
    hex = (hex.length === 1) ? '0' + hex : hex;
    hexStr += hex;
  }
  return hexStr.toUpperCase();
}

function hexStringToByte(str) {
  if (!str) return new Uint8Array();
  var a = [];
  for (var i = 0, len = str.length; i < len; i += 2)
    a.push(parseInt(str.substr(i, 2), 16));
  return new Uint8Array(a);
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function transceive(device, capdu) {
  let data = hexStringToByte(capdu);
  // send a command
  await device.controlTransferOut({
    requestType: 'vendor',
    recipient: 'interface',
    request: 0,
    value: 0,
    index: 1
  }, data);
  // wait for execution
  while (1) {
    resp = await device.controlTransferIn({
      requestType: 'vendor',
      recipient: 'interface',
      request: 2,
      value: 0,
      index: 1
    }, 1);
    if (new Uint8Array(resp.data.buffer)[0] === 0) break;
    await sleep(100);
  }
  // get the response
  resp = await device.controlTransferIn({
    requestType: 'vendor',
    recipient: 'interface',
    request: 1,
    value: 0,
    index: 1
  }, 1500);
  if (resp.status === "ok")
    return byteToHexString(new Uint8Array(resp.data.buffer));
  return '';
}
```
{{% /expand%}}

<script>
let connect = document.getElementById('connect');
let capdu = document.getElementById('capdu');
let send = document.getElementById('send');
let rapdu = document.getElementById('rapdu');
let info = document.getElementById('device-info');

function byteToHexString(uint8arr) {
  if (!uint8arr) return '';
  var hexStr = '';
  for (var i = 0; i < uint8arr.length; i++) {
    var hex = (uint8arr[i] & 0xff).toString(16);
    hex = (hex.length === 1) ? '0' + hex : hex;
    hexStr += hex;
  }
  return hexStr.toUpperCase();
}

function hexStringToByte(str) {
  if (!str) return new Uint8Array();
  var a = [];
  for (var i = 0, len = str.length; i < len; i += 2)
    a.push(parseInt(str.substr(i, 2), 16));
  return new Uint8Array(a);
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function transceive(device, capdu) {
  let data = hexStringToByte(capdu);
  // send a command
  await device.controlTransferOut({
    requestType: 'vendor',
    recipient: 'interface',
    request: 0,
    value: 0,
    index: 1
  }, data);
  // wait for execution
  while (1) {
    resp = await device.controlTransferIn({
      requestType: 'vendor',
      recipient: 'interface',
      request: 2,
      value: 0,
      index: 1
    }, 1);
    if (new Uint8Array(resp.data.buffer)[0] === 0) break;
    await sleep(100);
  }
  // get the response
  resp = await device.controlTransferIn({
    requestType: 'vendor',
    recipient: 'interface',
    request: 1,
    value: 0,
    index: 1
  }, 1500);
  if (resp.status === "ok")
    return byteToHexString(new Uint8Array(resp.data.buffer));
  return '';
}

connect.addEventListener('click', async () => {
  let device;
  try {
    device = await navigator.usb.requestDevice({ filters: [{
        classCode: 0xFF, // vendor-specific
    }]});
  } catch (err) {
    info.innerText = 'No device selected';
  }

  if (device !== undefined) {
    info.innerText = 'A CanoKey is selected';
    capdu.disabled = false;
    await device.open();
    await device.claimInterface(1);
    send.addEventListener('click', async () => {
      let resp = await transceive(device, capdu.value);
      rapdu.innerText = 'Response: ' + resp;
    });
  }
});
</script>
