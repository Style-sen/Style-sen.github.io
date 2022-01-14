# PLAT QT 基本模块 01 CORE


## 1. 设计要点

1. 提供了强大的`signals and slots`机制,用于对象之间的无缝通讯。
2. 可查询和可设计的对象属性。
3. 对象树。
4. 受保护的指针。
5. 跨库边界的动态转换。

## 2. 枚举值

```c++
//enum Qt::CheckState
Qt::Unchecked   //不选
Qt::PartiallyChecked    //半选
Qt::Checked     //选择
//enum Qt::ItemDataRole
Qt::CheckStateRole  //10 获取选中状态
//enum Qt::ItemFlag
Qt::ItemIsUserCheckable //16 可以被用户选中或不选
Qt::SplitBehavior//Qt::KeepEmptyParts|Qt::SkipEmptyParts
```

## 3. QAbstractItemModel(item model)抽象接口

1. 继承自`QObject`;

```c++
// virtual
data(const QModelIndex &index, int role = Qt::DisplayRole) const    //获取给定role的data；【渲染的时候调用】
flags(const QModelIndex &index) const   //返回index指定的item的flags，基类实现ItemIsEnabled和ItemIsSelectable【可以重写返回其他flags】
setData(const QModelIndex &index, const QVariant &value, int role = Qt::EditRole)   //设置给定role的data【界面修改的时候调用】
```

## 4. QAbstractTableModel表模型提供抽象结构

1. 继承自`QAbstractItemModel`;

```c++
//virtual
flags(const QModelIndex &index)
```

## 5. QByteArray字节数组

```c++
//实例方法
split()//以某个字符分割

```

## 6. QCoreApplication提供了一个事件循环

## 7. QDateTime

```c++
//static方法
currentDateTime()//本地时间
fromString()//从QString中获取时间，如hh:mm:ss.zzz
//实例方法
toMSecsSinceEpoch()//获取毫秒数
toTime_t()//被废弃，尽量不要用 +/1000 【会出问题】。 
```

## 8. QDebug 提供一个debug信息的输出流

## 9. QDir

```c++
/*实例方法*/
exists() // 判断文件夹是否存在
mkdir()  // 创建目录
```

## 10. QEvent事件类的基类

## 11. QFile 继承自 QFileDevice

```c++

```

## 12. QFileDevice

```c++

```

## 13. QHash字典

1. `QHash`的查找速度比`QMap`要快很多。
2. 在对`QHash`进行迭代时，这些项是任意排序的。在`QMap`中，项总是按键排序。
3. QHash的关键类型必须提供运算符`==()`和全局`QHash(key)`函数。`QMap`的关键类型必须提供操作符`<()`，以指定全序顺序。

```c++

```

## 14. QIODevice所有I/O设备的基础接口类

1. 继承自`QObject`;

## 15. QItemSelectionModel跟踪一个视图选择的项目

```c++
//实例方法
isRowSelected(int row, const QModelIndex &parent = QModelIndex())//整行的所有元素都选中，返回TRUE
```

## 16. QJsonArray

```c++
push_back() //放在队尾
```

## 17. QJsonObject json

1. 可以嵌套，可以配合`QJsonArray`一起使用。

```c++

```

## 18. QLinkedList链表

## 19. QList列表

1. 大多数情况下可以用QList。像prepend()和insert()这种操作，通常QList比QVector快的多。这是因为QList是基于index标签存储它的元素项在内存中，比那种依赖iterator迭代的更快捷。而且你的代码也更少。
2. 如果你需要一个真正的连接着的list，且需要保证一个固定插入耗时。那就用迭代器，而不是标签。使用QLinkedList();
3. 如果你需要开辟连续的内存空间存储，或者你的元素远比一个指针大，这时你需要避免个别插入操作，出现堆栈溢出，这时候用QVector
4. 如果你需要一个低层的可变数量大小的数组，用QVarLengthArray就够了。他可以预先在栈中分配已知长度大小的数组，如果超过这个长度，会在堆中继续存储。默认大小256

```c++
//实例方法
//增加
append(const T &value)
append(const QList<T> &value)
//查
at()//获取指定index的元素
indexof()   //
size()//返回节点的数目
count()//返回节点的数目
isEmpty()//空，返回true
//删除元素
removeOne(T)    //删除第一次出现的指定元素
//排序
qSort(s.begin(), s.end(),compare);//compare是排序回调函数
//遍历    
for(int i = 0; i < s.count() ; i++)
{
    qDebug() << s.at(i).num;
}
```

## 20. QMap 红黑树的字典

```c++
//实例方法
isEmpty()//判断是否为空
insert()//插入一个键值对
contains(key)//判断是否包含某个key
QMap<QString,QString>::iterator it = m_map.find("111"); //找到特定的“键-值”对  
it.value()//获取map里对应的值
//遍历
QMap<QString, QString>::iterator iterIndex = iter.valueOptions.begin();
while (iterIndex != iter.valueOptions.end())
```

## 21. QModelIndex在数据模型中定位数据

```c++

```

## 22. `QObject`【所有Qt类的基类】

```c++
setProperty(const char *name, const QVariant &value)//设置自定义属性（所有的类都可用），灵活方便
property(const char *name) const//获取到自定义的数据
sender()//获取到信号的发送者
timerEvent(QTimerEvent *event)//接收定时事件
```

## 23. QString（QT内部的编码方式 Unicode (utf-16)）

1. [真正的QString转char，utf8编码转gb2312编码](https://blog.csdn.net/xianjia4720/article/details/90516063).

```c++
//static方法
number(double n, char format = 'g', int precision = 6)//e科学计数法e；E科学计数法E；f浮点数；g:e或者f；G：E或者f
//实例方法
int compare(const QString &other, Qt::CaseSensitivity cs = Qt::CaseSensitive) const//返回小于等于大于0的整数
split()     //QString::SkipEmptyParts忽略空的部分，如：split(QRegExp(" *\""), QString::SkipEmptyParts)[0]
QString("%1%2").arg("").arg()   //格式化arg(hour, 2, 10, QLatin1Char('0'))补0
arg()//可以直接包含至多10个QString
arg(int a, int fieldWidth = 0, int base = 10, QChar fillChar = QLatin1Char(' ')) const//fieldWidth最小的填充空间量，正值是右对齐，负值是左对齐
setNum()    //数字转为QString
simplified()  //返回一个字符串，该字符串从开头和结尾删除了空格，并将每个内部空格序列替换为一个空格
trimmed()  //返回从开头和结尾删除空格的字符串。
QStringList split(const QString &sep, Qt::SplitBehavior behavior = Qt::KeepEmptyParts, Qt::CaseSensitivity cs = Qt::CaseSensitive) const
QStringList split(QChar sep, Qt::SplitBehavior behavior = Qt::KeepEmptyParts, Qt::CaseSensitivity cs = Qt::CaseSensitive) const
QStringList split(const QRegExp &rx, Qt::SplitBehavior behavior = Qt::KeepEmptyParts) const
QStringList split(const QRegularExpression &re, Qt::SplitBehavior behavior = Qt::KeepEmptyParts) const
/*转为char* */
Qstring str = "helloworld";
char *s;
QByteArray ba = str.toLatin1(); 
s = ba.data();
/*正则*/
constIterator = list.constBegin();//0
QRegExp rx("[A-Z]+");
int pos = 0;
if ((pos = (*constIterator).indexOf(rx) != -1)){
    qDebug() << rx.cap(0);
    constIterator++;//1
    qDebug() << (*constIterator).split(QRegExp(" *\""), QString::SkipEmptyParts)[0];
};
```

## 24. QStringList

1. QString调用split之后生成。

```c++
append()    //增加字符串或者<<
join()      //合并成一个字符串
/*遍历*/
QList<QString>::Iterator it = user.begin(),itend = user.end();
int i = 0;
for (;it != itend; it++,i++){
    if (*it == pFindLine->text()){//找到，高亮显示
        QModelIndex index = model->index(i);
        m_pTabList->setCurrentIndex(index);
        break;
    }
}
```

## 25. QTextCodec编码类

## 26. QThread多线程

1. QThread类提供了一个与平台无关的管理线程的方法。
2. 一个QThread对象管理一个线程。
3. 方法一：写一个类继承QThread，并重写其run()函数；run()函数是新线程的入口，run()函数退出，意味着线程的终止；使用start()函数来启动子线程，而不是run()；start()会自动调用run()；与主线程通过信号和槽进行通讯。
4. 方法二：使用`moveToThread()`。
5. 【注意】Qt中的UI操作，比如QMainWindow、QWidget之类的创建、操作，`只能位于主线程`！一般思路是，主线程负责提供界面，子线程负责无UI的单一任务，通过“信号-槽”与主线程交互。
6. QThread本身的成员都不属于新线程，而且在QThread构造函数里通过new得到的实例，也不属于新线程。这一特性意味着，如果要实现多线程操作，那么你希望属于新线程的实例、变量等，应该在run()中进行初始化、实例化等操作。
7. `qDebug() << "Current thread:" << thread();`用于判断是否在子线程。

## 27. QTime时钟时间

## 28. QTimer

1. 提供了重复和单次触发【信号】(非事件)的定时器。
2. 当QTimer的父对象被销毁时，它也会被自动销毁。
3. 定时器之间不是并行处理数据，而是分片占用主线程资源 ，且定时器完成其对应的slot函数任务后，下一个定时器才会开始执行其slot函数任务；
4. 当定时器的间隔时间小于其slot函数任务的执行时间时，一旦定时器timeout时，其slot函数不会终止执行，只是暂停，内部变量仍然保存，而后继续执行。
5. 把QTimer放进线程中的典型例子【此方法不能解决一个线程中使用两个定时器，因为他们关联到主线程的方法】：

    ```c++
    QThread* thread = new QThread(this);
    thread->start();
    
    QTimer *timer = new QTimer(0);//没有指定parent, 所以不会自动销毁.
    timer->setInterval(100);
    timer->moveToThread(thread);
    connect(timer, SIGNAL(timeout()), this, SLOT(onTimeout()), Qt::DirectConnection);//指定DirectConnection 才会直接调用slot. 即在worker线程中处理;否则connect默认参数为AutoConnection, 所以当slot的object是main线程时, 会自动post 事件到main线程
    connect(thread, SIGNAL(started()), timer,SLOT(start()));
    ```

6. 【不可以在子线程中调用UI方法（UI的事件在主线程），必须要用信号和槽】。

```c++
//实例方法
setInterval(int msec)//设置间隔，只的是上一个timeout执行完之后的间隔
```

## 29. QTimerEvent定时事件

1. 更高的效率。
2. 继承自`QEvent`;

## 30. QVariant通用数据类型的封装容器

1. The QVariant class acts like a union for the most common Qt data types. 

```c++
//构造函数
QVariant(int val)//增加一个整数的数据类型
//实例方法
toInt()
toFloat()
QString toString()
```

## 31. QVarLengthArray低级变长数组

## 32. QVector动态数组

1. 大多数情况下QVector应该是你的首选

```c++
append()    //增加元素
count()     //元素的数目 和size() 相同
clear()     //清空元素，qt5.6及以前会请空间，qt5.7开始不会清空间
currentRtuPara.paras.clear();
QVector<SProcValTableStruInfo_t>().swap(currentRtuPara.paras);
ins[]       //下标
/*遍历*/
QVector<QString>::iterator iter;  
for (iter=strArray.begin();iter!=strArray.end();iter++)  
    qDebug() <<  *iter << "\0"；
```

## 33. QXmlStreamReader
