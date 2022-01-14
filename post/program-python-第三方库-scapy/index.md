# PROGRAM PYTHON 第三方库 SCAPY



1. [Scapy](https://www.osgeo.cn/scapy/index.html)是功能强大的交互式数据包处理程序、数据包生成器、网络扫描器、网络发现工具和包嗅探工具。
2. 它能够伪造或解码各种协议的数据包，在线发送它们，捕获它们，匹配请求和答复等等.
3. 它可以轻松地处理大多数经典任务，例如扫描，跟踪路由，探测，单元测试，攻击或网络发现（它可以替代hping，nmap，arpspoof，arp-sofof，arp-sk，arping，tcpdump，tethereal，p0f等的85％）。
4. 它在大多数其他工具无法处理的许多其他特定任务上也表现出色，例如发送无效帧，注入您自己的802.11帧，结合技术（VLAN跳变+ ARP缓存中毒，WEP加密通道上的VOIP解码等等）等等。
5. `scapy主要做两件事：发送数据包和接收答案。定义一组数据包，发送它们，接收答案，将请求与答案匹配，并返回数据包对（请求、应答）列表和不匹配数据包列表`.

## 1. def 方法

### 1.1. `def sniff(*args, **kwargs)`

## 2. class 类

### 2.1. Packet 类

1. 定义一个协议(Packet的子类)即一个包：

```python
class Disney(Packet):
    name = "DisneyPacket " # 起个名字
    fields_desc=[ #字段组成
        ShortField("mickey",5),
        XByteField("minnie",3) ,
        IntEnumField("donald" , 1 ,
            { 1: "happy", 2: "cool" , 3: "angry" } )
        ]
```

#### 2.1.1. def 方法

1. `def __init__`直接传入bytes的字符串，即可进行解析，只解析能解析的，剩下的在load中。
1. `def show(self, dump=False, indent=3, lvl="", label_lvl="")`打印packet的结构

### 2.2. Field 类

1. `Field是Packet的成员`。
2. 认为是数据包的最小组成单元，在成帧的过程中，每一个filed输出一个bytes数组，然后packet按照顺序，把每个filed的bytes连接起来，形成最终的数据包。
3. 每个Field只需要关注自己输出的bytes数组就可以了，这也体现了面向对象的封装隔离的原则

#### 2.2.1. def 方法

1. `def addfield(self, pkt, s, val)`将Field封包成bytes string。
2. `def getfield(self, pkt, s)`将bytes string解包成Field。

#### 2.2.2. 子类

##### 2.2.2.1. `class BitField(Field)` 操作bit位

##### 2.2.2.2. `class ByteField(Field)` 字节

1. `def __init__(self, name, default)`名称、默认值

###### 2.2.2.2.1. 子类

####### 2.2.2.2.1.1. ‵class XByteField(ByteField)` 首选16进制

####### 2.2.2.2.1.2. `class LEThreeBytesField(ByteField)` 小端3字节

##### 2.2.2.3. `class DestField(Field)`

##### 2.2.2.4. `class FCSField(Field)`

##### 2.2.2.5. `class IntField(Field)`

##### 2.2.2.6. `class IP6Field(Field)`

##### 2.2.2.7. `class IPField(Field)`

##### 2.2.2.8. `class LEShortField(Field)` 小端无符号

##### 2.2.2.9. `class LESignedShortField(Field)` 小端有符号

##### 2.2.2.10. `class MACField(Field)`

##### 2.2.2.11. `class ShortField(Field)` 两个字节的短整形

##### 2.2.2.12. `class SignedByteField(Field)`

##### 2.2.2.13. `class SignedShortField(Field)`

##### 2.2.2.14. `class StrField(Field)`

###### 2.2.2.14.1. 子类

####### 2.2.2.14.1.1. `class PacketField(StrField)`
