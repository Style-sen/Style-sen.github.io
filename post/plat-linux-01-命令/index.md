# LINUX命令


（按照字母顺序）

## 1. busybox（常用工具包）

1. busybox是一个集成了一百多个最常用linux命令和工具的软件,甚至还集成了一个http服务器和一个telnet服务器,而所有这一切功能却只有区区1M左右的大小。
2. `一般可用来构建linux最小文件系统`;
3. 【支持的命令功能有限】。

## 2. cat(In busybox)

```shell
-n 或 --number 所有输出的行数编号
-b 或 --number-nonblank 和 -n 相似，只不过对于空白行不编号
-s 或 --squeeze-blank 当遇到有连续两行以上的空白行，就代换为一行的空白行
-v 或 --show-nonprinting
```

## 3. date(In busybox)

```shell
date +"%T.%12N" # 打印ps级别的时间，一般到纳秒
```

## 4. dhcp3-server

## 5. dirname

1. 输出去掉最后一个非斜杠目录名和尾随斜杠以后的剩余部分；如果目录名不包含/，则输出.（表示当前目录）.

```shell
dirname /usr/bin/  # 输出 /usr
dirname dir1/str  dir/str # 输出 dir1或者dir2
dirname stdio.h # 输出 .
```

## 6. ethtool（查询及设置网卡参数）

```shell
-a  查看网卡中接收模块RX、发送模块TX和Autonegotiate模块的状态：启动on 或 停用off。
-A  修改网卡中接收模块RX、发送模块TX和Autonegotiate模块的状态：启动on 或 停用off。
-c  display the Coalesce(聚合、联合) information of the specified ethernet card.聚合网口信息，使看起来更有规律。
-C  Change the Coalesce setting of the specified ethernet card.修改网卡聚合信息。
-g  Display the rx/tx ring parameter information of the specified ethernet card. 显示网卡的接收/发送环形参数。
-G  Change the rx/tx ring setting of the specified ethernet card. 修改网卡的接收/发送环形参数。
-i  显示网卡驱动的信息，如驱动的名称、版本等。
-d  显示register dump信息, 部分网卡驱动不支持该选项。
-e  显示EEPROM dump信息，部分网卡驱动不支持该选项。
-E  修改网卡EEPROM byte.
-k  显示网卡Offload参数的状态：on 或 off，包括rx-checksumming、tx-checksumming等。
-K  修改网卡Offload参数的状态
-p  用于区别不同ethX对应网卡的物理位置，常用的方法是使网卡port上的led不断的闪；N指示了网卡闪的持续时间，以秒为单位。
-r  如果auto-negotiation模块的状态为on，则restarts auto-negotiation.
-s  修改网卡的部分配置，包括网卡速度、单工/全双工模式、mac地址等。加上-s选项修改的内容才会生效
-S  显示NIC- and driver-specific 的统计参数，如网卡接收/发送的字节数、接收/发送的广播包个数等。
-t  让网卡执行自我检测，有两种模式：offline or online.
```

```shell
输出内容

```

## 7. exa（ls命令的现代替代品）

## 8. filezilla(FTP图形界面工具）)

## 9. find(In busybox)

1. `mtime`查找在指定时间曾被更改过的文件或目录，单位以24小时计算:+值,多少天之前,跟当前比较.

```shell
find . -exec COMMAND \; -exec COMMAND \; # find之后执行多条命令,使用{}指代每一行参数
-type # f 为文件
```

## 10. ftpget(In busybox)ftp下载文件

```shell

```

## 11. ftpput(In busybox)ftp上载文件

## 12. gpsd(GPS协议)

1. 自动探测并识别GPS协议，同时支持多个GPS设备，并且可以动态进行设备控制
2. 支持PPS并且与NTPD结合，实现时间服务
3. 统一生成JSON流报告给上层客户端
4. 支持DBUS，共享内存以及本地SOCKET等方式与客户端通讯，最大支持64个
5. 提供丰富的组件及工具用来调试及测试
6. 参考[GPSD架构介绍及交叉编译和使用](https://blog.csdn.net/wind0419/article/details/106686374).

## 13. grep(In busybox)

1. 一般用于管道对标准输出的内容进行检索。

```shell
-E或--extended-regexp                     使用正则表达式（双引号包起来）
-o 或 --only-matching                          只显示匹配PATTERN 部分。
```

## 14. hexdump(In busybox)

```shell
-n length 只格式化输入文件的前length个字节。
-C 输出规范的十六进制和ASCII码。
-b 单字节八进制显示。
-c 单字节字符显示。
-d 双字节十进制显示。
-o 双字节八进制显示。
-x 双字节十六进制显示。
-s 从偏移量开始输出。
-e 指定格式字符串，格式字符串包含在一对单引号中，格式字符串形如：'a/b "format1" "format2"'
    a表示对每a个输入字节应用format2格式
    b表示对每b个输入字节应用format1格式
    format1和format2中可以使用类似printf的格式字符串
```

## 15. hostapd

## 16. ipcs

```shell
-a #查看当前使用的共享内存、消息队列及信号量所有信息
-m #查看共享内存段
-p #pid
```

## 17. ipcrm

```shell
ipcrm [ -M key | -m id | -Q key | -q id | -S key | -s id ]
-M用shmkey删除共享内存
-m用shmid删除共享内存
-Q用msgkey删除消息队列
-q用msgid删除消息队列
-S用semkey删除信号灯
-s用semid删除信号灯
```

## 18. iperf3(带宽测试)

1. The ultimate speed test tool for TCP, UDP and SCTP.

### 18.1. 安装

1. [下载源码](https://iperf.fr/iperf-download.php#source);
2. `mkdir ../install_3.1.3`;
3. `./configure --host=arm-none-linux-gnueabi --prefix=${pwd}/../install_3.1.3`;
4. `make`;
5. `make install`.

### 18.2. 使用

## 19. iw（wifi配置工具）

1. Linux下的一种wifi配置工具，它替代了Wireless tools中的iwconfig.

## 20. less(In busybox)

## 21. more(In busybox)

```shell
+n                      #从笫n行开始显示
-n                      定义屏幕大小为n行
+/pattern       在每个档案显示前搜寻该字串（pattern），然后从该字串前两行之后开始显示
-c                      从顶部清屏，然后显示
-d                      提示“Press space to continue，’q’ to quit（按空格键继续，按q键退出）”，禁用响铃功能
-l                      忽略Ctrl+l（换页）字符
-p                      通过清除窗口而不是滚屏来对文件进行换页，与-c选项相似
-s                      把连续的多个空行显示为一行
-u                      把文件内容中的下画线去掉
```

## 22. nginx

1. 交叉编译见我的nginx代码库。

## 23. nohup(In busybox)

1. nohup命令是永久执行，忽略挂起信号。&是指在后台运行。
2. 用 & 后台运行程序时，如果是守护进程，断开终端则程序继续运行，如果不是守护进程，断开终端则程序也会被断开停止运行。
3. 使用nohup命令时，如果指定了输出文件，输出信息则会附加到输出文件中，如果没有指定输出文件，则输出信息会附加到当前目录下的nohup.out文件中，如果当前目录的nohup.out文件不可写，输出重定向到$HOME/nohup.out文件中。
4. `nohup ./program >/dev/null 2>log &`只输出错误信息到日志文件.
5. `nohup ./program >/dev/null 2>&1 &`什么信息也不要.

## 24. openssl

### 24.1. 交叉编译

1. `./config no-asm -shared --prefix=$(pwd)/.openssl`no-threads非多线程no-shared不支持动态链接库no-asm不在编译过程.中使用汇编代码加快编译过程
2. 修改Makefile:搜索-m64选项并删除，共两处;增加-lrt.
3. make
4. make install
5. 编译之后得到两个动态库`libcrypto`The libcrypto library provides the fundamental cryptographic routines used by libssl;You can however use libcrypto without using libssl.`libssl`.

## 25. setserial(In busybox)设置或显示串口的相关信息

1. `Usage: setserial [-gabGvzV] DEVICE [PARAMETER [ARG]]...`

```shell
-a   显示详细信息。
-b   显示摘要信息。
-g   显示串口的相关信息。
-G   以指令列表的格式来显示信息。
-q   执行时显示较少的信息。
-v   执行时显示较多的信息。
-V   显示版本信息。
-z   设置前，先将所有的标记归零。

PARAMETER
low_latency 以更高的CPU占用率来使得接受滞延达到最小。(通常传输中的5－10ms的滞延以使系统开销最小.) 此项默认值为关闭，但某些实时程序可能会用到此参数。
```

## 26. sort（文本内容排序）(In busybox)

1. Linux sort命令用于将文本文件内容加以排序。
2. sort可针对文本文件的内容，以行为单位来排序。

```shell
-b                  忽略每行前面开始出的空格字符。
-c                  检查文件是否已经按照顺序排序。
-d                  排序时，处理英文字母、数字及空格字符外，忽略其他的字符。
-f                   排序时，将小写字母视为大写字母。
-i                  排序时，除了040至176之间的ASCII字符外，忽略其他的字符。
-m              将几个排序好的文件进行合并。
-M                  将前面3个字母依照月份的缩写进行排序。
-n                  依照数值的大小排序。
-u                  意味着是唯一的(unique)，输出的结果是去完重了的。
-o<输出文件>                将排序后的结果存入指定的文件。
-r                  以相反的顺序来排序。
-t<分隔字符>                指定排序时所用的栏位分隔字符。
+<起始栏位>-<结束栏位>          以指定的栏位来排序，范围由起始栏位到结束栏位的前一栏位。
-k, --key=KEYDEF          sort via a key; KEYDEF gives location and type
```

## 27. squid（代理服务）

1. 安装完之后，编辑配置[使用linux服务器搭建代理](https://www.jianshu.com/p/189c5f37f255)。

## 28. stress（压力测试工具）

1. 可以对cpu、memory、IO以及磁盘进行压力测试

### 28.1. 安装

1. 下载`https://salsa.debian.org/debian/stress`;
2. 编译`./configure --prefix=/tmp/install --host=arm-linux-gnueabihf-`;
3. `make`;
4. `make instal`;

### 28.2. 使用

1. `-t --timeout N 指定运行N秒后停止`
2. `-c --cpu 产生n个进程 每个进程都反复不停的计算随机数的平方根`

## 29. stty（检查和修改当前注册的终端的通信参数）

## 30. tmux

在远程到一台机器或者在本地终端下，经常发现，一个终端远远地不够用，经常需要同时打开几个，往往还希望这几个窗口同时显示在视线内。tmux正是为这种需求而生。

### 30.1. 使用C/S模型构建

主要包括以下单元模块：

   1. server服务器。输入tmux命令时就开启了一个服务器。
   2. session会话。一个服务器可以包含多个会话
   3. window窗口。一个会话可以包含多个窗口。
   4. panel面板。一个窗口可以包含多个面板。

### 30.2. 命令

```shell
# server
tmux ls（Ctrl+b s）                      列出所有会话
tmux new -s <session-name>      新增一个会话
tmux rename-session -t 0 <new-name>（Ctrl+b $）     重命名会话
tmux detach  或者使用  exit（Ctrl+b d）    分离会话
tmux attach -t <session-name>  或者使用 tmux at -t <session-name>    重新连接会话
tmux kill-session -t <session-name>    杀死会话
tmux switch -t <session-name>     切换会话
# window
Ctrl+b c                     - (c)reate 生成一个新的窗口
Ctrl+b n                     - (n)ext 移动到下一个窗口
Ctrl+b p                     - (p)revious 移动到前一个窗口.
Ctrl+b & - 确认后退出 tmux 
# panel
Ctrl+b "                     - 水平分割当前panel
Ctrl+b %                     - 竖直分割当前panel
Ctrl+b 方向键                 - 在各窗格间切换
Ctrl+b；Ctrl+方向键           - 调整窗格大小
Ctrl+b q                     - 显示分隔panel的编号
Ctrl+b o                     - 跳到下一个分隔panel 
Ctrl+b x                     - 关闭当前panel
Ctrl+b 空格键                 - 采用下一个内置panel布局 

```

## 31. uniq（文本内容去重）(In busybox)

1. Linux uniq 命令用于检查及删除文本文件中重复出现的行列，因为他`只比较相邻的两行`，所以一般与 sort 命令结合使用。
2. uniq 可检查文本文件中重复出现的行列。

```shell
-c或--count                                                 在每列旁边显示该行重复出现的次数。
-d或--repeated                                          仅显示重复出现的行列。
-f<栏位>或--skip-fields=<栏位>          忽略比较指定的栏位。
-s<字符位置>或--skip-chars=<字符位置>                   忽略比较指定的字符。
-u或--unique                        仅显示出一次的行列。
-w<字符位置>或--check-chars=<字符位置>                      指定要比较的字符。

[输入文件] 指定已排序好的文本文件。如果不指定此项，则从标准读取数据；
[输出文件] 指定输出的文件。如果不指定此选项，则将内容显示到标准输出设备（显示终端）。
```

## 32. watchdog（看门狗）(In busybox)

1. 来操作和控制看门狗设备
2. -T:复位之后多少秒超时
3. -t:多少秒复位一次

## 33. wc（文件属性计数）(In busybox)

1. wc指令我们可以计算文件的Byte数、字数、或是列数。

```shell
-c或--bytes或--chars 只显示Bytes数。
-l或--lines 只显示行数。
-w或--words 只显示字数。
```

## 34. xargs给命令传递参数的一个过滤器

1. 是给命令传递参数的一个过滤器，也是组合多个命令的一个工具；
2. xargs 是一个强有力的命令，它能够捕获一个命令的输出，然后传递给另外一个命令；
3. xargs 一般是和管道一起使用`somecommand |xargs -item  command`。

```shell
-a file 从文件中读入作为 stdin
-e flag ，注意有的时候可能会是-E，flag必须是一个以空格分隔的标志，当xargs分析到含有flag这个标志的时候就停止。
-p 当每次执行一个argument的时候询问一次用户。
-n num 后面加次数，表示命令在执行的时候一次用的argument的个数，默认是用所有的。
-t 表示先打印命令，然后再执行。
-i 或者是-I，这得看linux支持了，将xargs的每项名称，一般是一行一行赋值给 {}，可以用 {} 代替。
-r no-run-if-empty 当xargs的输入为空的时候则停止xargs，不用再去执行了。
-s num 命令行的最大字符数，指的是 xargs 后面那个命令的最大命令行字符数。
-L num 从标准输入一次读取 num 行送给 command 命令。
-l 同 -L。
-d delim 分隔符，默认的xargs分隔符是回车，argument的分隔符是空格，这里修改的是xargs的分隔符。
-x exit的意思，主要是配合-s使用。。
-P 修改最大的进程数，默认是1，为0时候为as many as it can ，这个例子我没有想到，应该平时都用不到的吧。
```




