# 关于


<iframe width=100% height=500 src='/about.html'></iframe>

# 1. WHY

1. 建立自己的知识体系框架；
2. 此处维护一张目录，`1. `开头对应具体的文章。
3. 【核心的知识储备➕风口技术】

# 2. HOW

1. 使用`markdown`语法编写。
2. 可以使用`markmap插件`导出`html`文件，嵌入到`markdown`文件中`iframe`（嵌套网页）。

# 3. FOCUS

`学习 极客时间 《如何成为学习高手》`（4）

1. 如何成为学习高手。（F）

## 3.1. 项目管理

`学习 极客时间 雷蓓蓓（网易）《项目管理实战20讲》`（侧重于互联网项目）（完）

1. 如何管理一个项目。（F）

### 3.1.1. 归档

1. 编写软件项目需求分析文档。（F）
2. 编写软件项目设计方案文档。（F）
3. 注释规范（Doxygen）。（F）

### 3.1.2. 方法和工具

#### 3.1.2.1. git

1. git的使用。（F）
1. gitlab配置CI/CD。（F）
1. gitlab的CI/CD创建镜像。（F）

#### 3.1.2.2. 文档协作

1. Seafile和Onlyoffice搭建多人协作在线文档编辑。(F)
2. Nextcloud和Onlyoffice搭建多人协作在线文档。

## 3.2. HARDWARE

1. 常用电路。
2. 常用器件。(F)

### 3.2.1. 电机

`学习 B站 硬石科技的视频教程《基于STM32的电机》`

1. 直流无刷电机的驱动和控制。（F）
2. VESC开源电机项目学习。（F）
3. MikroKopter开源电机项目学习。（F）

### 3.2.2. STM32

1. 软件开发。(F)

## 3.3. PROGRAM

### 3.3.1. 跨语言

`学习《代码大全》`

1. 编码方式（转为字节流处理）
2. 开源协议。（F）
3. 基本数据类型。（F）
4. 编码规范。（F）

#### 3.3.1.1. 数据结构和算法

`学习 书籍 《数据结构算法与应用C++》`

#### 3.3.1.2. 设计模式

`学习 极客时间 王争 《设计模式之美》`
`学习 网站 https://refactoringguru.cn/`
`学习 GoF的《设计模式：可复用面向对象软件的基础》`（收藏）

1. 思想原则；（F）
2. 创建型；（F）
3. 结构型；（F）
4. 行为型。（F）
5. 跨设备。(F)

#### 3.3.1.3. 架构

`学习《架构整洁之道》`
`学习 电子书《开源软件架构》https://aosabook.org/en/index.html`
`学习 电子书 《Software Architecture Patterns》`

##### 3.3.1.3.1. 500 Lines or Less

1. DBDB: Dog Bed Database。（F）

##### 3.3.1.3.2. The Performance of Open Source Applications

##### 3.3.1.3.3. The Architecture of Open Source Applications（上）

1. nginx。（M）

##### 3.3.1.3.4. The Architecture of Open Source Applications（下）

##### 3.3.1.3.5. 其他开源项目

1. sqlitestudio项目源码分析。（F）
2. qtcreater项目源码分析。（F）

### 3.3.2. C/C++

`学习C++核心准则`

```shell
[注]
1. 全部（包括嵌入式和PC）使用C当然是最好的（nginx，sqlite，linux），但是在开发复杂项目的时候，C没有C++的优势（可读性、可复用性）。
2. 在嵌入式linux中，应用开发使用C++，但是只是使用C with class（如Guilite）。
3. 在嵌入式领域，尽量不使用STL，可以使用c实现复杂的数据结构和算法（难度较大）。
4. 在PC和服务器领域，则可以全面拥抱C++标准库（STL）/QSTL（QT）。
```

1. C-C++基础知识拾遗。（F）
2. C++面向对象。（F）
3. C-C++标准库。（F）
4. C-C++第三方库。（F）
5. C++核心准则。（F）
6. C++跨平台。（F）

#### 3.3.2.1. STL

1. STL简介；
2. STL中map的使用；

### 3.3.3. Python

`学习《极客时间》零基础学python`。（完）
`学习《极客时间》Python核心技术与实战`。（）

1. 开始一个python应用。（F）
2. python数据类型。（F）
3. python语法。（F）
4. 内建函数。（F）
5. 面向对象。（F）
6. 标准库。（F）
7. 并发编程。（F）
8. 网络编程。（F）
9. Kivy应用（Windows,Linux,Android,IOS,MAC OS）.

#### 3.3.3.1. Python第三方库

1. 第三方库（总）。（F）
2. `scapy`。（F）
3. `pandas`。（F）

##### 3.3.3.1.1. WEB框架

##### 3.3.3.1.2. 桌面应用(包括图形和客户端)

1. 打包应用和开源协议。（F）
1. python+qt.(F)

    ```markdown
    1. [pyqt](https://www.riverbankcomputing.com/software/pyqt/)
    2. [Qt for Python](https://doc.qt.io/qtforpython/)
    ```

##### 3.3.3.1.3. 数据分析

### 3.3.4. JS/TS

1. WebApi接口。（F）
2. JS混淆加密。
3. WEB插件。（F）
4. JS内置对象。（F）

#### 3.3.4.1. VUE

1. VUE生态（原理简介及扩展简介）
2. VUE基础

#### 3.3.4.2. 可视化

1. [Echarts](https://echarts.apache.org/examples/zh/index.html#chart-type-candlestick)应用。（F）

### 3.3.5. LUA（集成nginx）

1. lua基础知识
2. lua调用Nginx接口
3. lua常用模块。（F）
4. lua与C/C++相互调用

### 3.3.6. GO（使用GO开发嵌入式）

### 3.3.7. SQL

1. SQL。（F）

### 3.3.8. MarkDown

1. MarkDown基本语法。(F)

## 3.4. PLAT（即框架）

1. 软件框架是一个抽象的概念，是提供了通用的软件功能，可以通过用户编写代码有选择地改变，从而提供特定的软件应用。
2. 软件框架提供了【构建和部署应用程序】的标准方式。软件框架是一个通用的、可重用的软件环境，它提供特定的功能，作为大型软件平台的一部分，以促进软件应用程序、产品和解决方案的开发。
3. 软件框架可能包括**辅助程序**、**编译器**、**代码库**、**工具集API**，汇集了所有不同的**组件**，便于一个项目或系统的开发。

### 3.4.1. 编译

1. GCC。（F）
2. 构建框架（跨平台）。（F）

### 3.4.2. 嵌入式系统

1. 状态机。（F）

#### 3.4.2.1. Alios Things

#### 3.4.2.2. [BabyOS](https://gitee.com/notrynohigh/BabyOS)适用MCU裸机开发项目

#### 3.4.2.3. ChibiOS

#### 3.4.2.4. djyOS

#### 3.4.2.5. RT-Thread

#### 3.4.2.6. SylixOS

#### 3.4.2.7. Huawei LiteOS

#### 3.4.2.8. Nuttx

#### 3.4.2.9. Qnx

#### 3.4.2.10. VxWorks

#### 3.4.2.11. RTX

#### 3.4.2.12. mbed OS

#### 3.4.2.13. FreeRTOS

#### 3.4.2.14. eCos

#### 3.4.2.15. uc/OS-II

#### 3.4.2.16. uClinux

### 3.4.3. Linux/Embeded【系统】

1. Linux下常用命令和库函数。（F）
2. 调试和性能分析。（F）
3. Linux下进程守护。
4. Linux下shell编程。（F）
5. Linux系统定制（Yocto）。

#### 3.4.3.1. Linux应用程序编程

`学习 极客时间《网络编程实战》`()
`学习 书籍 《UNIX环境高级编程》`

1. 系统调用和标准库的封装（底层）。（F）
2. 网络编程。（M）

#### 3.4.3.2. Linux驱动程序编程

`学习 书籍 《嵌入式Linux应用开发完全手册》`

1. 字符设备驱动。（F）

#### 3.4.3.3. Linux方案实例（业务场景）

1. Linux下应用网页升级；
2. Linux双机备份；
3. LinuxRPC开发;
4. [linux 如何通过读取GPIO状态来实现失电检测](https://blog.csdn.net/u011983700/article/details/106923194)读取gpio状态的两种方式;
5. 时钟同步；（F）

##### 3.4.3.3.1. Linux应用日志管理

1. [参考](https://cloud.tencent.com/developer/article/1673078)

#### 3.4.3.4. OpenWrt（学习lua和web界面）

### 3.4.4. windows

1. [Dependency Walker 2.2](http://dependencywalker.com/)可以查看一个应用程序所依赖的库。

### 3.4.5. Qt【图形】

1. QT下载安装与配置及概览。（F）
2. QT IFW。（F）
3. 使用QT CREATER开发跨平台应用。（F）

### 3.4.6. 数据库

1. 实时数据库，内存数据库，关系型数据库概念及比较。（F）
2. redis.（F）
3. sqlite.（F）

### 3.4.7. web服务器

`学习 罗剑峰 《Nginx完全开发指南》`

1. nginx使用和源码分析。（F）

### 3.4.8. 中间件（消息系统，rpc）

1. zeromq-->nanomsg-->nng无代理消息传递（异步）。（F）
2. syslogd-->syslog-ng日志守护进程.(F)
3. Grpc（同步）。（F）

### 3.4.9. docker

### 3.4.10. Unity

1. 使用 Unity Editor 可创建 2D 和 3D 游戏、应用程序和体验。

### 3.4.11. [macchina.io](https://docs.macchina.io/edge/index.html)边缘计算网关

1. Macchina.io Edge是一种丰富的软件框架，可快速构建在基于Linux的设备上运行的IoT设备应用程序。Macchina.io Edge实现了启用Web的，安全，模块化和可扩展的JavaScript和C ++运行时环境，并提供即时使用和行业经过验证的软件构建块。这些使能设备与各种传感器，其他设备和云服务交谈，并在边缘设备或本地网络内本地处理，分析和过滤传感器数据。
2. GPL开源协议。

### 3.4.12. 其他平台

1. [LuatOS](https://gitee.com/openLuat/LuatOS)运行在嵌入式硬件,用户编写lua代码就可完成各种功能.
2. [AGL](https://www.automotivelinux.org/)汽车级Linux
3. [ROS](http://wiki.ros.org/)机器人操作系统。

## 3.5. 协议规范

1. modbus协议（M）。
2. iec61850[MZ Automation GmbH](https://github.com/mz-automation)
3. iec60870[libIEC61850 / lib60870-5](https://libiec61850.com/libiec61850/downloads/)

## 3.6. 生产力工具(TOOLS)

1. VS CODE。（F）
2. Visual Studio。（F）
3. CHROME。（F）
4. 语雀。
5. Xmind。
6. 截图工具-[Flameshot](https://flameshot.org/)
7. [Freeplane](https://www.freeplane.org/wiki/index.php/Linux)免费的思维导图软件。
8. vi非vim。（F）
9. 命令行终端（SHELL）。（F）
10. WireShark.(F)

