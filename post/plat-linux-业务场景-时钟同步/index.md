# 时钟同步


## 1. NTP

1. 毫秒级。5ms以内的精度。
2. 非超高精度设备，不建议使用PTP设备。
3. NTP是在应用层级别实现的.

### 1.1. ntp

1. NTP是用来使计算机时间同步化的一种协议，其同步时钟源不仅仅局限于网络的时间服务器，还包括时钟设备，如石英钟，原子钟，【GPS接收器】等。NTP服务器软件将这些时钟源抽象成相应的数据结构，对应于不同的内存地址，通过读取该地址中的信息，进行统计学算法的处理来同步计算机的时钟。

#### 1.1.1. 交叉编译

## 2. PTP

1. 微秒级。
1. PTP是在硬件级实现的(硬件支持时间辍，减少了应用到硬件的耗时误差).

### 2.1. ptpd

### 2.2. linuxptp

1. ptp4l:Implementation of PTP (Ordinary Clock, Boundary
Clock)
2. phc2sys:Synchronize two clocks (typically PHC and system clock)
3. pmc (PTP Management Client):Send PTP management messages to PTP nodes
4. [利用LinuxPTP进行时间同步(软/硬件时间戳)](https://blog.csdn.net/BUPTOctopus/article/details/86246335).

## 3. GPS

参考[基于GPS北斗卫星授时系统和NTP网络授时服务器的设计与开发](https://blog.csdn.net/qq_35382207/article/details/110203380)充分利用了NTP服务器软件对GPS时钟源的支持，采用[串行数据]和[秒脉冲(PPS微妙量级)]相结合的方式来校准时间，校时精度大为提高.

### 3.1. 与NTP结合方案

1. GPSD(GPS Daemon)是一个守护进程软件，用来处理GPS接收单元解码出的数据。基于GPSD综合校时的具体过程：GPS天线接收GPS信号，传递给G591芯片进行解码，每秒输出NMEA0183协议格式的数据和PPS信号，MAX 232完成电平转换之后，分别经由串口的RXD和DCD端传递给计算机；GPSD软件经过处理，将准确的时间信息写到特定内存段中；NTP服务器软件通过共享内存的方式读取该地址段中的时间信息，进而完成校正系统时钟的工作。
2. 参考[linux pps驱动实现gps授时](https://blog.csdn.net/liuxd3000/article/details/108983563)

### 3.2. GPS模块选型

| --  | JRC G591 | --  |
| --- | -------- | --- |
