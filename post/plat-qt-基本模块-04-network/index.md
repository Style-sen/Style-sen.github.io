# QT-NETWORK


使网络编程更容易和更可移植性.`QT += network`

## QAbstractSocket

1. 继承自`QIODevice`;

## 1. Qftp

1. 需要自己编译。

    ```shell
    # LINIX
    1. 下载源码`https://github.com/qt/qtftp`;
    2. `mkdir build`;
    3. `qmake`;
    4. `cd build`；
    5. `make -f ../Makefile`;
    6. `make -f ../Makefile install`;
    7. 使用`Qt += ftp`;
    # WINDOWS
    1. 使用QCreater工具打开；
    2. 选择编译工具；
    3. 需要安装perl运行环境。
    4. 更改qtftp.pro，CONFIG += staticlib CONFIG += shared，这样会生成lib文件。
    # 编译完成之后需要将生成的库文件和头文件，保存到QT库中
    1. 将 Qt5Ftpd.lib Qt5Ftp.lib Qt5Ftpd.prl Qt5Ftp.prl 拷贝至 D:\Qt\Qt5.5.1\5.5\msvc2010\lib；
    2. 将Qt5Ftpd.dll Qt5Ftp.dll 拷贝至D:\Qt\Qt5.5.1\5.5\msvc2010\bin 
    3. 将qftp.h qurlinfo.h 拷贝至 D:\Qt\Qt5.5.1\5.5\msvc2010\include\QtNetwork,并新建一个名为 QFtp 的文件(没有后缀名),然后用本写入 #include "qftp.h"。
    ```

2. 依赖qt消息循环系统，所以需要指定一个parent。
3. 可以调度的操作也被称为“命令”有connectToHost()、login()、close()、list()、cd()、get()、put()、remove()、mkdir()、rmdir()、rename() 和 rawCommand()。
4. 所有这些命令都会返回一个唯一的标识符允许跟踪当前正在执行的命令。当命令的执行开始时发出带有命令标识符的 commandStarted() 信号。当命令完成时会发出 commandFinished() 信号并带有命令标识符和一个 bool 参数表明该命令在完成时是否出错。
5. 【注】：ftp服务端检测，如果长时间不操作，会自动断开连接。

```c++
connectToHost() //连接到服务端
login() //登录
state() //当前状态
//信号
commandStarted()    // 命令开始信号
commandFinished()   // 命令完成信号
done()              // 多个命令最后一个完成
listInfo()          // list命令会发射这个信号，对于发现的每个条目都会发射此信号
dataTransferProgress() // 显示进度条
```

## QTcpSocket
