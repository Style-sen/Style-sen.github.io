# 嵌入式-NRF-BLE



NRF开源的库从底层到顶层。

## [NordicSemiconductor/pc-ble-driver](https://github.com/NordicSemiconductor/pc-ble-driver)

`C\C++`

[最新版本V4.1.1](https://github.com/NordicSemiconductor/pc-ble-driver/releases/tag/v4.1.1)


提供C/C++库for Bluetooth Low Energy nRF5 SoftDevice serialization.可以直接调用，也可以使用其上层封装（js和python版）。
pc-ble-driver是Nordic提供的一套开源的上位机BLE开发软件库，配合nrf52832开发板，可以在上位机实现BLE中央设备和外设的功能。上位机与硬件板之间通过UART通信，通信协议类似于蓝牙的HCI。在硬件设备中，运行基于softdevice的connectivity程序，与上位机的UART进行交互。
pc-ble-driver提供C/C++、Python和Node.js三种源码库，

## [NordicSemiconductor/pc-ble-driver-js](https://github.com/NordicSemiconductor/pc-ble-driver-js)

`NODEJS`

Node.js interface to the C/C++ pc-ble-driver library. `被nrfconnect使用通过nrf52840 dongle扫描和连接和DFU`。

## [pc-nrf-dfu-js](https://github.com/NordicSemiconductor/pc-nrf-dfu-js)

`NODEJS`

pc-nrf-dfu-js is a Javascript module which provides DFU (Device Firmware Upgrade) via USB CDC ACM transport for Nordic devices.通过ACM口来更新Nordic设备的固件。

```
import bleDriver from 'pc-ble-driver-js'
//创建adapter实例
import { AdapterFactory, FirmwareRegistry } from 'pc-ble-driver-js';
const adapter = adapterFactory.createAdapter(sdVersion, comName, instanceId);
 adapter.close(error => {});
//创建DFU
dfu = new bleDriver.Dfu('BLE', {
            adapter,
            targetAddress: device.address,  //MAC地址
            targetAddressType: device.addressType,    //'BLE_GAP_ADDR_TYPE_RANDOM_STATIC' or `BLE_GAP_ADDR_TYPE_PUBLIC`.
            bondingData: getBondingData(),
        });
setupListeners(dispatch);

function setupListeners(dispatch) {
    dfu.on('transferStart', fileName => {
        dispatch(transferFileStartedAction(fileName));
    });
    dfu.on('transferComplete', fileName => {
        dispatch(transferFileCompletedAction(fileName));
    });
    dfu.on('progressUpdate', progress => {
        dispatch(updateProgressAction(progress));
    });
    dfu.on('logMessage', onLogMessage);
}
function updateProgressAction(progressInfo) {
    return {
        type: UPDATE_PROGRESS,
        percentCompleted: progressInfo.percentCompleted,
        status: progressInfo.stage,
        bytesPerSecond: progressInfo.bytesPerSecond,
        averageBytesPerSecond: progressInfo.averageBytesPerSecond,
        completedBytes: progressInfo.completedBytes,
        totalBytes: progressInfo.totalBytes,
    };
}
function removeListeners() {
    dfu.removeAllListeners('transferStart');
    dfu.removeAllListeners('transferComplete');
    dfu.removeAllListeners('progressUpdate');
    dfu.removeAllListeners('logMessage');
}
dfu.performDFU(filePath, (error, aborted) => {
            if (error) {
                dispatch(performDfuError(error));
                dispatch(showErrorDialog({ message: error.message }));
            } else if (aborted) {
                dispatch(abortSuccessAction());
            } else {
                dispatch(performDfuSuccessAction());
            }

            // DFU done. The adapter might still be connected to the device,
            // if the DFU failed or was aborted. Make sure the adapter is
            // disconnected from the device before handing back control of
            // events to nRF Connect.
            disconnectFromDevice(adapter, device.address).then(() => {
                dispatch(enableDeviceEvents(device.address));
            });
            // For unbonded Buttonless DFU:
            disconnectFromDevice(adapter, addressPlusOne).then(() => {
                dispatch(enableDeviceEvents(addressPlusOne));
            });
        });
```

## [NordicSemiconductor/pc-nrfconnect-rssi](https://github.com/NordicSemiconductor/pc-nrfconnect-rssi)

`NODEJS`

`nrfconnect app`

RSSI数据的可视化. The app shows dBm per frequency in the 2400-2480 MHz range, and allows the user to tweak settings like sweep delay and animation duration. The app can be installed from the Add/remove apps screen in nRF Connect.

The following devices are supported:

PCA10040 nRF52832 Development Kit
PCA10059 nRF52840 Dongle

## [pc-nrfconnect-ble](https://github.com/NordicSemiconductor/pc-nrfconnect-ble)

`NODEJS`

`nrfconnect app`

BLE连接库（管理与其他ble设备的连接）
nRF Connect Bluetooth® Low Energy is a cross-platform tool that enables testing and development with Bluetooth Low Energy (previously called Bluetooth Smart). It allows easy setup of connections with other devices and use these connections for reading and writing to the external nodes.

nRF Connect Bluetooth Low Energy is implemented as an app for nRF Connect.
The app supports the following devices:
* PCA10028 nRF51 Development Kit
* PCA10031 nRF51 Dongle
* PCA10040 nRF52 Development Kit
* PCA10056 nRF52840 Development Kit
* PCA10059 nRF52840 Dongle

## [pc-nrfconnect-core](https://github.com/NordicSemiconductor/pc-nrfconnect-core)

可在此框架上创建自己的APP

nRF Connect is跨平台的框架运行APPfor creating desktop apps for use with development kits or dongles from Nordic Semiconductor. It provides a common foundation for creating apps that communicate with the devices *ver serial port*,The framework comes with a skeleton(包含一些基础UI组件) that has standard UI components for selecting devices, navigation menus, logging, etc. Apps can decorate the standard components, create new components, and use built-in libraries in order to create end-user tools.

nRF Connect supports the following devices:
* PCA10028 nRF51 Development Kit
* PCA10031 nRF51 Dongle
* PCA10040 nRF52 Development Kit
* PCA10056 nRF52840 Development Kit
* PCA10059 nRF52840 Dongle

Note that while the nRF Connect framework has support for all these devices, the device support in nRF Connect apps may vary.
