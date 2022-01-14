# 嵌入式-ESP32


## [《ESP32技术参考手册》](chrome-extension://cdonnmffkdaoajfknoeeecmchibpmkmg/static/pdf/web/viewer.html?file=https%3A%2F%2Fwww.espressif.com%2Fsites%2Fdefault%2Ffiles%2Fdocumentation%2Fesp32_technical_reference_manual_cn.pdf)

1. 双CPU，两个CPU的名称分别是“PRO_CPU”和“APP_CPU”；
2. 存储
```
片上存储器
    –448 KB Internal ROM
    –520 KB Internal SRAM
    –8 KB RTC FAST Memory
    –8 KB RTC SLOW Memory
片外存储器
    片外SPI存储器可作为片外存储器被映射到可用的地址空间。部分片上存储器可用作片外存储器的Cache。
        –最大支持16 MB片外SPI Flash
        –最大支持8 MB片外SPI SRAM
```
3. ESP32 芯片包含两个硬件定时器组。每组有两个通用硬件定时器。它们都是基于 16 位预分频器和 64 位自动重载功能的向上/向下计数器的 64 位通用定时器。
### 启动过程

1. 在esp32 上电运行后，芯片运行的第一个程序。这段程序是芯片设计与生产的时候，固化在硬件电路中的。所以它是不可修改的(Read Only Memory)。
2. esp32 的 ROM 负责检测芯片的strapping配置，来决定芯片应该处于什么状态。比如，esp32 上电后，ROM 程序会检查 [GPIO0, GPIO2, GPIO4, MTDO, GPIO5]的状态。
```
如果 GPIO0 / GPIO2 同时为低电平，则会进入下载模式，等待串口通信信息。
如果GPIO0为高电平，则会进入Flash 运行模式，启动SPI 驱动，并加载Flash中的程序段。
```
3. 关于启动模式
```
BOOT_MODE[5:0]:

pull-up, pull-down, pull-down, pull-up, pull-up, SW4 /5/4/3/2/1/ )  
[GPIO0, GPIO2, GPIO4, MTDO, GPIO5]  
1 x x x x --> SPI Boot  
0 0 x x x --> Download Boot (Jonit-Detection of UART0+UART1+SDIO_Slave)  
下载模式的串口输出如下(115200), ROM默认会输出当前所处的模式。  
rst:0x1 (POWERON_RESET),boot:0x3 (DOWNLOAD_BOOT(UART0/UART1/SDIO_REI_REO_V2))   
waiting for download  

其中，boot:0x3 表示的是芯片strapping pin脚的状态，   
0x03对应 [GPIO0, GPIO2, GPIO4, MTDO, GPIO5] 的值为 [ 0, 0, 0, 1, 1]  
所以处于 Download Boot 模式。  
```
4. 关于下载模式

可以使用[esptool](https://github.com/espressif/esptool)脚本，可以进行寄存器的读写，固件下载，程序运行等操作。

5. 关于STUB

在ROM模式，由于芯片处于低频工作的状态，通信速率受限。在esptool中，会将一段小程序加载到esp32的RAM中，并跳转执行RAM中的小程序。这段小程序包含了ROM中相同的串口通信协议，并对其进行了扩充。

6. 关于 Flash Boot 模式

如果芯片启动时，GPIO0为高电平，芯片会进入Flash 运行模式。 此时，启动SPI 驱动，并加载Flash中的程序段。ROM 会读取外置 Flash 的0x1000地址，加载并运行二级bootloader。

7. 关于二级Bootloader

二级bootloader可以认为是一个独立的小程序，二级bootloader会对芯片频率进行初始化，并且读取系统SPI的配置信息，对Flash 运行模式以及频率进行配置，然后根据分区表的定义，从对应的地址加载应用程序，并且运行应用程序固件。

## 硬件家族

硬件代码|类型|描述
--|--|--
ESP32 DEVKITV1 | 开发板 | [PINOUT-DOIT ESP32 Devkit V1](https://github.com/playelek/pinout-doit-32devkitv1)，[原理图]()

## 开始编程

[ESP-IDF 编程指南](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/index.html)

1. 构建toolchain；
2. 配置ESP-IDF；
3. 设置环境变量
4. 开始一个工程，工程目录下：
```
idf.py menuconfig   # 配置
idf.py build    # build
idf.py -p PORT flash    # flash
idf.py -p PORT monitor    # 监控  Ctrl+]退出
```
5. 升级ESP-IDF，使用git命令，直接pull即可，注意子模块的更新。

### 了解[编译系统Cmake](https://docs.espressif.com/projects/esp-idf/zh_CN/stable/api-guides/build-system-cmake.html)

Read this document if you want to know how to organise and build a new ESP-IDF project or component using the CMake-based build system.\

1. 一个工程是各种组件的融合。
2. project 是一个包含文件和配置的目录；
3. 工程配置放在工程根目录下的sdkconfig文件中，通过idf.py menuconfig来进行修改。一个工程只能有这一个文件。
4. 一个工程通常构建两个APP，一个是project app，一个是bootloader app。
5. 组件是独立的代码库，通称是以静态库(.a)提供。

#### 工程目录

```
- myProject/
     - CMakeLists.txt  # cmake规则文件
     - sdkconfig    # idf.py menuconfig 自动生成
     - components/ - component1/ - CMakeLists.txt
                                 - Kconfig
                                 - src1.c
                   - component2/ - CMakeLists.txt
                                 - Kconfig
                                 - src1.c
                                 - include/ - component2.h
     - main/ - Kconfig.projbuild
             - CMakeLists.txt    # 组件规则
             - src/
                 - app_main.c
                 - module1/
                 - module2/
             - inc/
                 - module1/
                 - module2/
     - build/    # idf.py menuconfig 自动生成
```
#### main的Kconfig.projbuild文件

```
# 开始一个菜单
menu "菜单名称"
    # 开始一个配置
    config 配置名（全大写） # 对应CONFIG_配置名
        bool  "配置描述"  # bool类型，选中即定义，不选中即没有定义（可使用#ifdef）
        default "n"（"y"）

        int   "配置描述"  # 整数类型，
        range 0 10  # 整数范围
        default 5  # 默认整数

        string  "配置描述"  # 字符串类型
        default ""  

        help
            xxxxxxxxx  # 帮助信息

        depends on 配置名  # 依赖配置名

        if 配置名
            menu

            endmenu
        elif
            menu
                
            endmenu
        endif
    choice 配置名
        prompt "描述"

    endchoice

endmenu
```

#### 项目CMakeLists 文件

必要
```
cmake_minimum_required(VERSION 3.5)    //必须放在 CMakeLists.txt 文件的第一行，它会告诉 CMake 构建该项目所需要的最小版本号
include($ENV{IDF_PATH}/tools/cmake/project.cmake)    //会导入 CMake 的其余功能来完成配置项目、检索组件等任务。
project(myProject)    //会创建项目本身，并指定项目名称
```
选用
```C
COMPONENT_DIRS    //组件的搜索目录，默认为 ${IDF_PATH}/components、${PROJECT_PATH}/components 和 EXTRA_COMPONENT_DIRS。如果您不想在这些位置搜索组件，请覆盖此变量。
EXTRA_COMPONENT_DIRS    //用于搜索组件的其它可选目录列表。路径可以是相对于项目目录的相对路径，也可以是绝对路径。
COMPONENTS    //要构建进项目中的组件名称列表，默认为 COMPONENT_DIRS 目录下检索到的所有组件。使用此变量可以“精简”项目以缩短构建时间。请注意，如果一个组件通过 COMPONENT_REQUIRES 指定了它依赖的另一个组件，则会自动将其添加到 COMPONENTS 中，所以 COMPONENTS 列表可能会非常短。
COMPONENT_REQUIRES_COMMON    //每个组件都需要的通用组件列表，这些通用组件会自动添加到每个组件的 COMPONENT_PRIV_REQUIRES 列表中以及项目的 COMPONENTS 列表中。默认情况下，此变量设置为 ESP-IDF 项目所需的最小核心“系统”组件集。通常您无需在项目中更改此变量。
```


#### 组件CMakeLists 文件

最小的
```
set(COMPONENT_SRCS "foo.c")   # 用空格分隔的源文件列表（*.c，*.cpp，*.cc，*.S），里面所有的源文件都将会编译进组件库中
set(COMPONENT_ADD_INCLUDEDIRS "include")    # 用空格分隔的目录列表，里面的路径会被添加到所有需要该组件的组件（包括 main 组件）全局 include 搜索路径中
register_component()
```

#### 组件中使用第三方 CMake 项目



### 研究API参考

#### Event Loop Library

#### peripherals

##### I2C

```
esp_err_ti2c_set_timeout(i2c_port_ti2c_num, int timeout)    //设置I2C的超时时间 第一个参数为 I2C的port number 第二个参数的单位为APB 80Mhz的时钟周期
esp_err_ti2c_get_timeout(i2c_port_ti2c_num, int *timeout)    //读取I2C的超时间 第一个参数为 I2C的port number 第二个参数为存储结果的指针
```

### 研究头文件

头文件|描述
--|--
bt.h|configures the BT controller and VHCI from the host side.
esp_bt_main.h|initializes and enables the Bluedroid stack.
esp_gap_ble_api.h|implements the GAP configuration, such as advertising and connection parameters.
esp_gattc_api.h|implements the GATT Client configuration, such as connecting to peripherals and searching for services.


### 研究Example

#### bluetooth

##### gatt_client

#### get-started

##### blink

#### peripherals

##### ADC

##### I2C

###### i2c_self_test

###### i2c_tools

1. 是开发I2C设备的有用的工具。
3. 默认 i2c 0，SDA（18）  SCL（19）
2. 提供命令行：
```
 ==============================================================
 |       Steps to Use i2c-tools on ESP32                      |
 |                                                            |
 |  1. Try 'help', check all supported commands               |
 |  2. Try 'i2cconfig' to configure your I2C bus              |
 |  3. Try 'i2cdetect' to scan devices on the bus             |
 |  4. Try 'i2cget' to get the content of specific register   |
 |  5. Try 'i2cset' to set the value of specific register     |
 |  6. Try 'i2cdump' to dump all the register (Experiment)    |
 |                                                            |
 ==============================================================
```

##### spi_slave

###### sender



##### timer_group(硬件定时器)

1. 使用队列；
2. timer_group。
3. 参考[ESP32 学习笔记（十）Timer](https://blog.csdn.net/qq_27114397/article/details/81636607).

##### uart

###### uart_async_rxtxtasks

1. 创建freertos任务

```
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

xTaskCreate(rx_task, "uart_rx_task", 1024*2, NULL, configMAX_PRIORITIES, NULL);
xTaskCreate(tx_task, "uart_tx_task", 1024*2, NULL, configMAX_PRIORITIES-1, NULL);

```

#### system

##### esp-timer

1. 虽然FreeRTOS提供了软件定时器，但是这些定时器有一些限制：最大分辨率等于系统滴答周期；回调函数是从低优先级分派的。
2. 硬件定时器不受上述限制，但是又很难用，所以出现了esp-timer。
```C
esp_timer_get_time()    //
```
3. 休眠模式
```

```

### 扩展

#### [AT命令](https://github.com/espressif/esp32-at)

1. 参考上述工程，可将AT命令集加入到自定义工程中。
2. at Cmakefiles
```
set(COMPONENT_ADD_INCLUDEDIRS include)
set(COMPONENT_REQUIRES main)
register_component()
```
3. main Cmakefiles 链接静态库（.a）
```
register_component()
target_link_libraries(main "-L../components/at/lib")
target_link_libraries(main at_core)
```

#### 使用PN532模块

1. 参考[redchenjs/nfc_attendance_system_esp32](https://github.com/redchenjs/nfc_attendance_system_esp32)

## MicroPython

1.  参考[Quick reference for the ESP32](https://docs.micropython.org/en/latest/esp32/quickref.html)
2. [MicroPython-ESP32基础教程](http://www.1zlab.com/wiki/micropython-esp32/emp-ide-userguide/)


