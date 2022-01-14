# 嵌入式-MicroBit-概述


1. 对战机甲。

## C/C++

1. 基于`Mbed`，可以使用[线上IDE](ide.mbed.com).
2. [接口文档](https://lancaster-university.github.io/microbit-docs/ubit/)
3. The micro:bit DAL is built on top of ARM mbed and hence uses yotta as an offline build system.

### YOTTA

1. Yotta is a  `module management system for C++ and C` designed to make it easier to build better software by re-using code.
2. We built yotta for Mbed OS, but you can use it for projects on other platforms too. 
3. [yotta教程](http://docs.yottabuild.org/tutorial/tutorial.html).
4. Yotta构建两种东西：模块和可执行文件。


### API


### Bluetooth

#### [Bluetooth Developer Studio](https://docs.bluetooth.com/developerstudio/index.html)


## JS(Makecode)

1. Microsoft MakeCode是一个用于创建JavaScript/积木块在线编辑器的框架。
2. Microsoft MakeCode(基于PXT)是用户编辑器中的名称，而PXT在 GitHub 的源码中使用。
3. MakeCode可将代码转成用于下载的hex文件（`UF2文件格式`）。
4. UF2是一种文件格式，由Microsoft为PXT(也称为Microsoft MakeCode)开发，特别适合通过MSC(海量存储类;又名可移动闪存驱动器)烧写微控制器。

### [文档](https://makecode.microbit.org/docs)

1. `模块都是全局变量，直接调用`。

```js
bluetooth.onBluetoothConnected(function () {
    basic.showLeds(`
        . # # # .
        . . . # .
        . # # # .
        . # . . .
        . # # # .
        `)
})
bluetooth.onBluetoothDisconnected(function () {
    basic.showLeds(`
        . . # . .
        . . # . .
        . . # . .
        . . # . .
        . . # . .
        `)
})
bluetooth.startLEDService()
basic.showLeds(`
    . # # # .
    . # . . .
    . # # # .
    . . . # .
    . # # # .
    `)
basic.forever(function () {
	
})
```

#### basic

#### bluetooth

1. 安卓手机的话，只有三星能够正常配对（据说还有小米），因此，你在编写 MicroBit的代码时，`务必使用非配对的蓝牙模式`。
2. `可以使用makecode编写Ble程序`。
```
```
##### LED Service


## MicroPython

1. [MicroPython文档](https://microbit-micropython.readthedocs.io/en/v1.0.1/)

### Bluetooth

1. Microbit的硬件支持BLE。MicroPython中的BLE协议栈要占12kb的RAM，而MicroBit的RAM只有16kB，没有足够的空间。
2. 所以使用radio模块，比BLE简单，但是在教育场景下已够用。

