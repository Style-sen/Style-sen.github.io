# 嵌入式-NFC


## NFC类型

### NFC-A

基于ISO/IEC 14443A。类似RFID-A。AM调制弥勒编码（高低代表1和0）。106 Kbps数据速率。

### NFC-B

基于ISO/IEC 14443B。类似RFID-B。AM调制曼彻斯特编码（边沿触发）。106 Kbps数据速率。

### NFC-F

基于FeliCA JIS X6319-4。更快。也被称作FeliCA。它被用于各种各样的应用，如基于creadit卡或借记卡的支付，地铁车票的访问控制，在办公室和住宅公寓的个人识别等。

## NFC标签类型(TAG)

参考[MIFARE Type Identification Procedure](https://www.nxp.com/docs/en/application-note/AN10833.pdf)可以看到ATQA和SAK与卡类型的对应关系。

|type|Standard|Memory Size|Speed|Data Access|Collision mechanism|Compliant Products|Pricing|
|--|--|--|--|--|--|--|--|
|Type-1|ISO-14443A|96 bytes|106 Kbps|Read/Write or Read Only|Data collision protection not available|Innovision Topaz|Low|
|Type-2|ISO-14443A|48 bytes/ 144 bytes	|106 Kbps	|Read/Write or Read Only	|Anti-collision support available	|NXP Mifare Ultralight, NXP Mifare Ultralight C	|Low	|
|Type-3|ISO-18092, JIS-X-6319-4 |1/4/9 KB	|212 or 424 kbps |Read/Write or Read Only	| Anti-collision support available	|Sony Felica	|High	|
|Type-4|NXP DESFire Tag (ISO-14443A) |4 KB/ 32 KB |106/212/424 Kbps |Read/Write or Read Only	|Anti-collision support available	| NXP DESFire, NXP SmartMX-JCOP	|Medium to high	|
|Type-5 Tag (Type Mifare Classic Tag)| ISO-14443A, MF1 IC S50 |192 Bytes/ 768 bytes/ 3584 bytes |106Kbps |Read/Write or Read Only	|Anti-collision Support available |NXP Mifare Classic 1K, NXP Mifare Classic 4K, NXP Mifare Classic Mini	|Low	|

### 

## APDU

1. 智能卡与智能卡读卡器之间传送的信息单元, 是给智能卡发送的命令。

### IFD（读卡接口设备）与IC卡之间的通讯：

1. 首先，IFD发送5个字节的命令头给IC卡，这5个字节的内容告诉了IC卡要做什么操作；
2. 然后，IC卡回传一个过程字节给IFD，告诉IFD，IC卡是否支持该操作；
3. 如果返回的过程字节告诉IFD——IC卡支持该操作，则接下来就进行数据的传输（如果有的话），数据从IFD输入到IC卡或数据从IC卡输出到IFD；
4. 如果返回的过程字节告诉IFD——IC卡不支持该操作，则IC卡回传给IFD两个状态字节SW1 SW2，IFD根据SW1 SW2两字节判断IC卡不支持该操作的原因；

### 命令头

|  |  |  |
|--|--|--|
| CLA | 指令类别 | 其中值‘FF’被保留用于PPS。注意 ISO/IEC 7816-4 强制‘FF’对于CLA为无效值 |
| INS | 指令码 | 其中值‘6X’和‘9X’为无效值。注意 ISO/IEC 7816-4 强制‘6X’和‘9X’对于INS为无效值 |
| P1 | 参数1 | |
| P2 | 参数2 | |
| Lc | Data的长度 | =0 |
| Data |  | |
| Le | 希望响应的数据的长度 | |

### 指令类别和指令码

|指令|类别|指令码|功能|例子|参数1|参数2|
|--|--|--|--|--|--|--|
|SELECT|00|A4|选择文件||
|READ BINARY|00|B0|读二进制文件内容|0x00, 0xb0, 0x00, 0x00, 0x10 读取16个字节|
|READ RECORD|00|B2|读记录文件指定内容||
|UPDATE BINARY|00/04|D6|写二进制文件|
|UPDATE RECORD|00/04|DC|写记录文件|
||00|a4|选择文件||0x04（根据名称选择）;0x00（根据file id选择）|0x00（根据名称选择）；0x0C（根据file id选择）|

### 状态码

|状态码| 十进制表示 |性质|错误解释|
|--|--|--|
|9000 | 144， 0|正常| 成功执行|
|6200 |警告 信息未提供
|6281 |警告 回送数据可能出错
|6282 |警告 文件长度小于Le
|6283 |警告 选中的文件无效
|6284 |警告 FCI格式与P2指定的不符
|6300 警告 鉴别失败
|63Cx 警告 校验失败（x－允许重试次数）
|6400 出错 状态标志位没有变
|6581 出错 内存失败
|6700 出错 长度错误
|6882 出错 不支持安全报文
|6981 出错 命令与文件结构不相容，当前文件非所需文件
|6982 出错 操作条件（AC）不满足，没有校验PIN
|6983 出错 认证方法锁定，PIN被锁定
|6984 出错 随机数无效，引用的数据无效
|6985 出错 使用条件不满足
|6986 出错 不满足命令执行条件（不允许的命令，INS有错）
|6987 出错 MAC丢失
|6988 出错 MAC不正确
|698D 保留
|6A80 出错 数据域参数不正确
|6A81 出错 功能不支持；创建不允许；目录无效；应用锁定
|6A82 | 106,130 |出错 |该文件未找到|
|6A83 出错 该记录未找到
|6A84 出错 文件预留空间不足
|6A86 出错 P1或P2不正确
|6A88 出错 引用数据未找到
|6B00 出错 参数错误
|6Cxx 出错 Le长度错误，实际长度是xx
|6E00 出错 不支持的类：CLA有错
|6F00 出错 数据无效
|6D00 出错 不支持的指令代码
|9301 出错 资金不足
|9302 出错 MAC无效
|9303 出错 应用被永久锁定
|9401 出错 交易金额不足
|9402 出错 交易计数器达到最大值
|9403 出错 密钥索引不支持
|9406 出错 所需MAC不可用
|6900 出错 不能处理
|6901 出错 命令不接受（无效状态）
|61xx 正常 需发GET RESPONSE命令
|6600 出错 接收通讯超时
|6601 出错 接收字符奇偶错
|6602 出错 校验和不对
|6603 警告 当前DF文件无FCI
|6604 警告 当前DF下无SF或KF

## NDEF

参考[haldean/ndef](https://github.com/haldean/ndef)

### 读写TAG 4

使用pn532仿真tag4获得此流程。
```C
Emulating NDEF tag now, please touch it with a second NFC device
    // 第一步：NDEF Tag Application Select Procedure 根据名称选择
    In: 00  a4  04  00  07  d2  76  00  00  85  01  01  00  
    Out: 90  00  
    // 第二步：根据file_id选择
    In: 00  a4  00  0c  02  e1  03  
    Out: 90  00  
    // 读
    In: 00  b0  00  00  0f  
    Out: 00  0f  20  00  54  00  ff  04  06  e1  04  ff  fe  00  00  90  00  
    In: 00  a4  00  0c  02  e1  04  
    Out: 90  00  
    In: 00  b0  00  00  02  
    Out: 00  21  90  00  
    In: 00  b0  00  02  21  
    Out: d1  02  1c  53  70  91  01  09  54  02  65  6e  4c  69  62  6e  66  63  51  01  0b  55  03  6c  69  62  6e  66  63  2e  6f  72  67  90  00  
    In: 00  a4  00  0c  02  e1  03  
    Out: 90  00  
    In: 00  b0  00  00  0f  
    Out: 00  0f  20  00  54  00  ff  04  06  e1  04  ff  fe  00  00  90  00  
    In: 00  a4  00  0c  02  e1  04  
    Out: 90  00  
    In: 00  b0  00  00  02  
    Out: 00  21  90  00  
    // 写第一步
    In: 00  d6  00  00  02  00  00  
    Out: 90  00  
    // 写第二步
    In: 00  d6  00  02  16  d1  01  12  54  <数据>  
    Out: 90  00  
    // 写第三步
    In: 00  d6  00  00  02  00  16  
    Out: 90  00  
    In: 03  b0  00  00  01  
nfc_emulate_target: Success
```

## 读卡器

### [ACR122U](http://www.acr122.com/)

1. 支持Android™ OS 3.1 及以上版本（使用OTG）。

### PN532

PN532是一个高度集成的非接触读写芯片，它包含80C51微控制器内核，集成了13.56MHz下的各种主动/被动式非接触通信方法和协议。
PN532传输模块支持6种不同的工作模式：
* 读写器模式，支持ISO/IEC 14443A / MIFARE®机制
* 读写器模式，支持 FeliCa机制
* 读写器模式，支持ISO/IEC 14443B机制
* 卡操作模式，支持ISO 14443A / MIFARE®机制
* 卡操作模式，FeliCa机制
* ISO/IEC18092，ECM340点对点

#### 用户手册

##### 命令支持

|a |b |c |d |e |f |g |h |i |j |k |
|--|--|--|--|--|--|--|--|--|--|
|0x00| 0x00| 0xFF| LEN(数据长度+1)|LCS(LEN的校验)| 0xD4|CC|Optional Input Data|DCS|0x00|
00 00 FF LEN LCS D5 CC+1 Optional Output Data DCS 00

###### 读取PN532版本

发送：0x02
返回：0x03 0x32 0x01 0x06 0x07

###### InDataExchange

支持读卡器（PN532）和TAG之间的协议数据（APDU）交流。



#### [Libnfc](http://nfc-tools.org/index.php?title=Libnfc)

##### Ubuntu下使用

1. 安装完libnfc;
2. 更改配置：`/etc/nfc/libnfc.conf`
```
device.name = "microBuilder.eu"
device.connstring = "pn532_uart:/dev/ttyUSB0"
```

modules

##### NFC Device/Hardware manipulation

[API](http://www.libnfc.org/api/modules.html)

nfc/drivers/pn532_i2c.c
```
static nfc_device * pn532_i2c_open(const nfc_context *context, const nfc_connstring connstring)

```
nfc/nfc/nfc-internal.c
```
int connstring_decode(const nfc_connstring connstring, const char *driver_name, const char *bus_name, char **pparam1, char **pparam2)
```
nfc/nfc/nfc.c
```C
void nfc_init(nfc_context ** context)    // context	Output location for nfc_context
// (必须第一步)Initialize libnfc. This function must be called before calling any other libnfc function.
size_t nfc_list_devices(nfc_context * context,    // The context to operate on, or NULL for the default context.
                        nfc_connstring connstrings[],    // array of nfc_connstring.
                        const size_t connstrings_len     // size of the connstrings array.
)    // (可选)扫描支持的设备 (ie. only available for some drivers)返回找到的设备数量
nfc_device* nfc_open(nfc_context * context,    // The context to operate on.
                     const nfc_connstring connstring     // The device connection string if specific device is wanted, NULL otherwise
)    // Open a NFC device.Returns pointer to a nfc_device struct if successfull; otherwise returns NULL value.

int nfc_initiator_init(nfc_device * pnd)    // Initialize NFC device as initiator (reader)读卡器

int nfc_initiator_list_passive_targets	(	nfc_device * 	pnd,    // nfc_device struct pointer that represent currently used device
                                            const nfc_modulation 	nm,    // desired modulation
                                            nfc_target 	ant[],    // array of nfc_target that will be filled with targets info
                                            const size_t 	szTargets     // size of ant (will be the max targets listed)
)    // List passive or emulated tags.	Returns the number of targets found on success, otherwise returns libnfc's error code (negative value)

int nfc_initiator_select_passive_target	(	nfc_device * 	pnd,    // nfc_device struct pointer that represent currently used device
                                            const nfc_modulation 	nm,    //desired modulation
                                            const uint8_t * 	pbtInitData,  // optional initiator data, NULL for using the default values.
                                            const size_t 	szInitData,    // pbtInitData的长度.
                                            nfc_target * 	pnt    // nfc_target struct pointer which will filled if available
)	// 选择一个tag  Returns selected passive target count on success, otherwise returns libnfc's error code (negative value)

// 执行下边的函数必须先选择target
int nfc_initiator_transceive_bytes	(	nfc_device * 	pnd,    // nfc_device struct pointer that represent currently used device
                                        const uint8_t * 	pbtTx,    // 待发送帧的字节数组
                                        const size_t 	szTx,    // 待发送帧的字节数组的长度
                                        uint8_t * 	pbtRx,    // 用来存储从卡中读取的数据字节
                                        const size_t 	szRx,    // size of pbtRx (Will return NFC_EOVFLOW if RX exceeds this size)
                                        int 	timeout    // in milliseconds
)	// Returns received bytes count on success, otherwise returns libnfc's error code
//The NFC device (configured as initiator) will transmit the supplied bytes (pbtTx) to the target. It waits for the response and stores the received bytes in the pbtRx byte array.
//If NP_EASY_FRAMING option is disabled the frames will sent and received in raw mode: PN53x will not handle input neither output data.
//The parity bits are handled by the PN53x chip. The CRC can be generated automatically or handled manually. Using this function, frames can be communicated very fast via the NFC initiator to the tag.
//Tests show that on average this way of communicating is much faster than using the regular driver/middle-ware (often supplied by manufacturers).
//WARNING:The configuration option NP_HANDLE_PARITY must be set to true (the default value).
//NOTE:When used with MIFARE Classic, NFC_EMFCAUTHFAIL error is returned if authentication command failed. You need to re-select the tag to operate with.
//NOTE:If timeout equals to 0, the function blocks indefinitely (until an error is raised or function is completed) If timeout equals to -1, the default timeout will be used

```

#### [Libfreefare](http://nfc-tools.org/index.php/Libfreefare)基于libnfc访问MIFARE TAG

##### 支持部分卡

Tag	| Status
--|--
FeliCa Lite	Supported
MIFARE Classic 1k |	Supported
MIFARE Classic 4k |	Supported
MIFARE DESFire 2k |	Supported
MIFARE DESFire 4k |	Supported
MIFARE DESFire 8k |	Supported
MIFARE DESFire EV1 |	Supported
MIFARE Mini	| Supported
MIFARE Plus S 2k|	Not supported
MIFARE Plus S 4k|	Not supported
MIFARE Plus X 2k|	Not supported
MIFARE Plus X 4k|	Not supported
MIFARE Ultralight|	Supported
MIFARE Ultralight C|	Supported
NTAG21x	|Supported

##### 源码分析

freefare.c
```C
MifareTag * freefare_get_tags (nfc_device *device)    // 
// send NFC commands to the initiator to prepare communication with a MIFARE card, and perform required cleannups after using the targets.
// Get a list of the MIFARE targets near to the provided NFC initiator.
// The list has to be freed using the freefare_free_tags() function.
// 此方法在nfc_open之后就可以调用
```

### MFRC522

#### [非接触CPU卡读卡模块|Mifare plus读写模块|CUT100-PLUS](https://item.taobao.com/item.htm?spm=a1z02.1.2016030118.d2016038.584d782dU6GTzP&id=41402998243&scm=1007.10157.81291.100200300000000&pvid=b35f93ad-75f1-48fa-842e-301016fcb583)

#### [周立功 ZLG522S/T RFID 读卡模块 13.56MHz 可直拍](https://item.taobao.com/item.htm?spm=a230r.1.14.9.67656a7dnBcuzZ&id=41191367328&ns=1&abbucket=12#detail)

## 模拟标签

### NRF52832

NFCT(Near field communication tag)是NFC-A类型。

|寄存器|功能|
|--|--|
|TXD.FRAMECONFIG|设置发送数据帧的格式|
|RXD.FRAMECONFIG|设置接收数据帧的格式|

#### SDK

##### nrf5_sdk/components/nfc/t4t_lib/nfc_t4t_lib.h

 This is the NFC Forum NDEF tag 4 type emulation library. It implements the ISO14443-4A protocol (ISO-DEP) and additionally can emulate a read-writable NDEF content. If the emulation of the NDEF content is not needed, the library works in a raw mode where all APDUs are delivered to the user, who is then responsible to generate a timely RPDU as a response.

 The sequence of initializing functions determines whether the NDEF emulation will run or whether the raw mode is used.
* E.g. NDEF emulation
```
         * @ref nfc_t4t_setup
         * @ref nfc_t4t_ndef_rwpayload_set or @ref nfc_t4t_ndef_staticpayload_set
         * @ref nfc_t4t_emulation_start
         * ... running in NDEF emulation mode ...
```
* E.g. RAW mode
``` 
         * @ref nfc_t4t_setup
         * @ref nfc_t4t_emulation_start
         * ... running in RAW emulation mode ...
```

##### nrf5_sdk/components/nfc/t2t_lib/nfc_t2t_lib.h

NFC Forum type 2 tag.

## 缩略语

|||
|--|--|
|PICC|proximity card，感应卡|

