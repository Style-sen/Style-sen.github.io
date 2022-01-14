# MicroPython


## 文件结构

### 官方固件

```
/
    boot.py
```

1. _boot.py在文件系统中不可见（在MicorPython中称为internal frozen modules，也就是编译好的内部模块）。这个模块主要完成的功能包括创建/加载文件系统，在首次启动时初始化网卡配置等。不建议对这个模块进行任何变动。
2. MicroPython启动之后首先执行boot.py文件，此文件是由MicroPython系统自己创建的，除非很了解micropython，否则不要修改此文件；
3. 紧接着会执行`main.py`文件，由用户自己创建。没有此文件，启动会报错`OSError: [Errno 2] ENOENT`;
4. 不要将大量代码写入main.py文件，将要完成的功能分模块写于多个文件中。

### EMP

```
/
    boot.py  # 启动代码
    lib/
        
    config/
```

## 最佳实践

### 写中断处理程序

1. 尽可能短和简单；
2. 避免内存分配:没有添加到列表或插入字典，没有浮点数。
3. 使用 `micropython.schedule` 解决上述约束.
4. 如果一个ISR返回多字节，使用预先分配的`bytearray`. 如果ISR和主程序共享多字节整数，请使用`array (array.array)`.
```
The simplest means of doing this is via one or more shared data objects, either declared as global or shared via a class。

import pyb, micropython
micropython.alloc_emergency_exception_buf(100)
class Foo(object):
    def __init__(self, timer, led):
        self.led = led
        timer.callback(self.cb)
    def cb(self, tim):
        self.led.toggle()

red = Foo(pyb.Timer(4, freq=1), pyb.LED(1))
green = Foo(pyb.Timer(2, freq=0.8), pyb.LED(2))

The use of instance methods confers two benefits. Firstly a single class enables code to be shared between multiple hardware instances. Secondly, as a bound method the callback function’s first argument is self. This enables the callback to access instance data and to save state between successive calls.
```
The MicroPython library I/O methods usually provide an option to use a pre-allocated buffer.
5. 进入中断程序之后，先disable中断，处理完之后，再enable中断。
6. Allocate an emergency exception buffer。在中断处理程序中加入以下代码，调试会变简单。
```
import micropython
micropython.alloc_emergency_exception_buf(100)
```

### 最大化microPython速度

### 上传下载文件

1. 使用webrepl上传和获取文件。

## 特有模块

### EMP-1ZLAB

EasyMicroPython(EMP) IDE 是由1Z实验室引导的一个开源项目，旨在为MicroPython社区提供更高效便捷的开发体验。EMP IDE受到了MicroPython/webrepl 项目的启发，采用了其现有的通信协议，并对webrepl模块进行了改造，设计并实现了一些功能模块，以此来通过WebSocket实现和MicroPython设备的更丰富，更高效的信息交互。

### MACHINE

允许你直接和无限制地使用硬件模块。

`所有的回调方法都应该在中断上下文中`

#### CLASS

##### ADC

```python
class machine.ADC(id=0, *, bits=12)    使用指定的GPIO口创建ADC实例或不指定
# ADC.channel(id, *, pin)    ESP32的ADC没有这个方法
# ADC.init()    ESP32的ADC没有这个方法
# ADC.deinit()   ESP32的ADC没有这个方法
ADC.read()    读取一次采样值
# 设置D34号引脚作为ADC采样引脚
pin_read = Pin(34,Pin.IN)
# 声明ADC对象
adc = ADC(pin_read)
# 设置衰减比 满量程3.3v
adc.atten(ADC.ATTN_11DB)
# 设置数据宽度为10bit
adc.width(ADC.WIDTH_10BIT)
```

##### PIN

##### SIGNAL

##### Timer

###### PROPERTY

名称|类型|描述
--|--|--
Timer.PERIODIC| |周期执行
Timer.ONE_SHOT| |只执行一个，执行完了定时器就结束

#####  UART
```
class machine.UART(id, ...)
    id 给定的id(即串口号)
UART.init(baudrate=9600, bits=8, parity=None, stop=1, *, ...)
    baudrate 波特率
    bits 位数
    parity  极性
    stop  停止位
    tx  指定发送的引脚（即GPIO号）
    rx  制定接收的引脚（即GPIO号）
    txbuf  指定发送的缓冲区大小
    rxbuf  指定接收的缓冲区大小
UART.deinit()    关闭串口
UART.any()    非阻塞地返回可以读的字符数目，有可能返回1，但是有多于1的字符
UART.read([nbytes])    读取指定数目的字符，不指定则尽可能多读
UART.readline()    读取一行
UART.write(buf)    写数
UART.sendbreak()
UART.irq(trigger, priority=1, handler=None, wake=machine.IDLE)    创建一个回调当有数接收时
    trigger  只能是UART.RX_ANY
    priority  优先级（1-7）  
    handler  回调函数（1-8个字节）
    wake  只能是machine.IDLE
```
### NETWORK

#### CLASS

##### WLAN

WIFI驱动

###### METHODS

名称|示例|描述
--|--|--
activate([is_active])|
connect(ssid=None, password=None, *, bssid=None)|connect('ssid','password') | 只有客户端模式才允许连接

#### PROPERTY

名称|类型|描述
--|--|--
STA_IF|WLAN参数|连接到上游的wifi热点
AP_IF|WLAN参数|wifi热点，允许设备连接


#### METHODS


### UPIP

microPython的包管理

### REPL

||||
|--|--|--|
| Ctrl + A | 进入RAW REPL |
| Ctrl + B | 进入NORMAL REPL |
| Ctrl + C | 中断程序 | 如果没有出现">>>"可以使用
| Ctrl + D | 软复位板子|
| Ctrl + E | 进入paste模式 |

#### 使用AMPY

```
pip install adafruit-ampy --upgrade
上传文件
ampy --port /dev/ttyUSB0 put main.py
删除文件
ampy --port /dev/ttyUSB0 rm main.py  
```
