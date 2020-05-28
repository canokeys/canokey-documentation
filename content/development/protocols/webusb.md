---
title: "WebUSB"
date: 2019-11-28T10:18:29-05:00
weight: 50
---

### 1. Overview

CanoKey supports the [WebUSB](https://wicg.github.io/webusb/) protocol for easier management. You can use our open-source [console](http://) to manage your CanoKey. Or, you can develop your own console via WebUSB APIs.

#### 1.1 Driver

CanoKey is a [WinUSB device](https://docs.microsoft.com/en-us/windows-hardware/drivers/usbcon/automatic-installation-of-winusb), whose firmware defines certain Microsoft operating system (OS) feature descriptors that report the compatible ID as "WINUSB". Therefore, you do not need additional drivers when you use it on Windows. And of course, no drivers are required on Linux or macOS.

#### 1.2 Interface & Pipe

CanoKey uses the interface with index 1, and the transfer type is control transfer. The default EP size is 16 bytes.

### 2. Messages

Basically, the messages on the WebUSB interface are APDU commands. To transceive a pair of APDU commands, two phases are required:

1. Send a command APDU
2. Get the response APDU

Each type of message is a vendor-specific request, defined as:

| bRequest | Value |
|----------|-------|
| CMD      | 00h   |
| RESP     | 01h   |
| STAT     | 02h   |

#### 2.1 Command APDU

The following control pipe request is used to send a command APDU.

| bmRequestType | bRequest | wValue | wIndex | wLength        | Data  |
| ------------- | -------- | ------ | ------ | -------------- | ----- |
| 01000001B     | CMD      | 0000h  | 1      | length of data | bytes |


#### 2.2 Get the response APDU

The following control pipe request is used to get the response APDU.

| bmRequestType | bRequest | wValue | wIndex | wLength | Data |
| ------------- | -------- | ------ | ------ | ------- | ---- |
| 11000001B     | RESP     | 0000h  | 1      | 0       | N/A  |

The device will send the response no more than 1500 bytes.

#### 2.3 Get the execution status

The following control pipe request is used to get the status of the card.

| bmRequestType | bRequest | wValue | wIndex | wLength | Data |
| ------------- | -------- | ------ | ------ | ------- | ---- |
| 11000001B     | STAT     | 0000h  | 1      | 0       | N/A  |

The response data is 1-byte long, 0x01 for in progress and 0x00 for finishing processing and you can fetch the result using `RESP` command, and other values for invalid states.

{{% notice note %}}
If the command is still under processing, the response will be empty.
{{% /notice %}}

### 3. Demo Code

If you have CanoKey, you can try it now.

#### 3.1 Connect

Click the following button to connect a CanoKey.

<button id="connect" class="btn btn-default">Connect</button>
<span id="device-info"></span>

{{%expand "Show me the code"%}}
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

#### 3.2 Transceive

Once you connect your CanoKey, input the command APDU here, e.g., `00A4040005F000000000`, then click "Send".

<style>
input[disabled] {
color: rgb(84, 84, 84);
background-color: rgb(235, 235, 228);
}
</style>
<input class="form-element-input" type="text" id="capdu" placeholder="Command APDU" disabled>
<button id="send" class="btn btn-default">Send</button>
<p id="rapdu">Response: </p>

{{%expand "Show me the code"%}}
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
