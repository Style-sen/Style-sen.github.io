# NODEJS-外围设备模块


## NFC

### [nfc-pcsc](https://www.npmjs.com/package/nfc-pcsc)
```
node:>=8
```
1. 分析源码
```
NFC类（继承自EventEmitter）
|----emit reader(接收到pcsc的信号)
    |----reader类（参数）
        |----async write(blockNumber, data, blockSize = 4)
        |----async read(blockNumber, length, blockSize = 4, packetSize = 16, readClass = 0xff)
        |----async authenticate(blockNumber, keyType, key, obsolete = false)
        |----async loadAuthenticationKey(keyNumber, key)
        |----control(data, responseMaxLength)
        |----transmit(data, responseMaxLength)
        |----connect(mode = CONNECT_MODE_CARD)
        |----disconnect()
		|----emit error
		|----emit card.off    //卡离开读卡器
		|----emit card
		|----emit end
|----emit error
```
2. 参考[pokusew/electron-vue-nfc]例子(https://github.com/pokusew/electron-vue-nfc)
3. 可以用于Electron APP，但是[需要使用NODE原生模块](https://electronjs.org/docs/tutorial/using-native-node-modules)。
4. 注：`运行包含此模块的应用时，需要一个NFC读卡器硬件插到电脑上，激活读卡器服务。否则运行程序会出现空白的情况。`
5. 使用此模块读取ISO 14443-4 tags时，需要进行额外的配置：
```
    // enable when you want to auto-process ISO 14443-4 tags (standard=TAG_ISO_14443_4)
	// when an ISO 14443-4 is detected, SELECT FILE command with the AID is issued
	// the response is available as card.data in the card event
	// you can set reader.aid to:
	// 1. a HEX string (which will be parsed automatically to Buffer)
	// reader.aid = 'F222222222';
	// 2. an instance of Buffer containing the AID bytes
	// reader.aid = Buffer.from('F222222222', 'hex');
	// 3. a function which must return an instance of a Buffer when invoked with card object (containing standard and atr)
	//    the function may generate AIDs dynamically based on the detected card
	// reader.aid = ({ standard, atr }) => {
	//
	// 	return Buffer.from('F222222222', 'hex');
	//
	// };
```
6. [Ubuntu使用ACR122U参考](https://oneguyoneblog.com/2016/11/02/acr122u-nfc-usb-reader-linux-mint/)

## USB

### [usb](https://www.npmjs.com/package/usb)
1. 在安装usb之前，需要安装libusb
```
Ubuntu/Debian: sudo apt-get install build-essential libudev-dev
Windows：Use Zadig to install the WinUSB driver for your USB device. Otherwise you will get LIBUSB_ERROR_NOT_SUPPORTED when attempting to open devices.
```
2. `rebuild usb`需要使用原生模块

## BLE(使用CSR4.0)

### [noble/noble](https://github.com/noble/noble)

#### windows

```
npm install --global --production windows-build-tools
在工程目录下
npm install bluetooth-hci-socket
npm install noble
```
A `WinUSB` driver is required, use [Zadig tool](https://zadig.akeo.ie/) to replace the driver for your adapter.

### Nordic Semiconductor（NRF）

参见[NRF-BLE]


## 其他

### [Cfonts]()
性感的终端字体。
### [chalk](https://github.com/chalk/chalk)
终端字型。
### [Electron](https://www.npmjs.com/package/electron)
打造桌面应用。
### [seafile](https://www.npmjs.com/package/seafile)
不好用，可以自己参照github，自己实现。
### [yargs/yargs]
Yargs通过解析参数和生成优雅的用户界面，帮助您构建交互式命令行工具。


## 错误集锦

1. [fatal error LNK1107](https://github.com/node-ffi/node-ffi/issues/272)
2. [Error in node module: fatal error LNK1107](https://cmsdk.com/node-js/error-in-node-module-fatal-error-lnk1107-invalid-or-corrupt-file-cannot-read-at-0x2be03e.html)




