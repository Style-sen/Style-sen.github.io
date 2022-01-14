# python-标准库



## 1. __main__

## 2. argparse 被 CLICK 代替

## 3. base64

## 4. COLLECTIONS

## 5. datetime

### 5.1. 属性

1. datetime.MINYEAR（1）和datetime.MAXYEAR（9999）。
2. 

### 5.2. 类

#### 5.2.1. date

##### 5.2.1.1. 实例属性

1. year、month、day。

#### 5.2.2. time

##### 5.2.2.1. 实例属性

1. hour, minute, second, microsecond。

#### 5.2.3. datetime

##### 5.2.3.1. 类方法

```python
now() # 当前时刻
```

##### 5.2.3.2. 实例方法

```python
dt.strftime( '%Y-%m-%d %H:%M:%S %f' )
```

#### 5.2.4. timedelta 时间间隔

## 6. enum

1. `from enum import Enum,IntEnum,unique`

```python
# 导入枚举类
from enum import Enum
 
# 继承枚举类
class color(Enum):
    YELLOW  = 1
    BEOWN   = 1 
    # 注意BROWN的值和YELLOW的值相同，这是允许的，此时的BROWN相当于YELLOW的别名
    RED     = 2
    GREEN   = 3
    PINK    = 4
 
class color2(Enum):
    YELLOW  = 1
    RED     = 2
    GREEN   = 3
    PINK    = 4
```

2. 枚举类不能用来实例化对象.
3. 访问枚举类中的某一项，直接使用类名访问加上要访问的项即可，比如 color.YELLOW.
4. 枚举类里面定义的Key = Value，在类外部不能修改Value值，也就是说下面这个做法是错误的.
5. 枚举项可以用来比较，使用==，或者is.
6. 导入Enum之后，一个枚举类中的Key和Value，Key不能相同，Value可以相，但是Value相同的各项Key都会当做别名.
7. 如果要枚举类中的Value只能是整型数字，那么，可以导入IntEnum，然后继承IntEnum即可，注意，此时，如果value为字符串的数字，也不会报错
8. 如果要枚举类中的key也不能相同，那么在导入Enum的同时，需要导入unique函数.

## 7. file

## 8. hashlib不同的安全散列和消息摘要算法

```python
>>> import hashlib
>>> hashlib.algorithms_available
{'whirlpool', 'md5', 'shake_256', 'BLAKE2s256', 'SHA224', 'blake2s256', 'BLAKE2b512', 'md5-sha1', 'md4', 'sha256', 'sha3_224', 'sha3_384', 'shake_128', 'blake2b512', 'SHA512', 'SHA1', 'sha224', 'RIPEMD160', 'sha3_256', 'SHA384', 'MD5-SHA1', 'sha3_512', 'SHA256', 'MD4', 'sha1', 'sha512', 'MD5', 'sha384', 'blake2s', 'ripemd160', 'blake2b'}
>>> hashlib.algorithms_guaranteed
{'sha224', 'sha3_256', 'sha384', 'md5', 'shake_256', 'sha256', 'blake2s', 'sha3_224', 'blake2b', 'sha3_384', 'shake_128', 'sha3_512', 'sha1', 'sha512'}
>>> m=hashlib.sha512()
>>> m.update('helloword')
>>> m.update(b'helloword')  # 不能是unicode编码   
>>> m.hexdigest()
'c3710d9949a3ee2b1c89ec9e9dd154a649aeecc43422222ce3d4de51fa32f021e75123a1b5769aae35c939bf87f333d92ec58192ce5f55fa72e344225027c16e'
```

## 9. hmac实现了 HMAC 算法

## 10. html

## 11. json

## 12. logging

## 13. math



## 14. os

### 14.1. def 方法

```python
os.walk([path]) # 遍历path
def getAllSub(path):
    Dirlist = []
    Filelist = []
    for home, dirs, files in os.walk(path):
        # 获得所有文件夹
        for dirname in dirs:
            Dirlist.append(os.path.join(home, dirname))
        # 获得所有文件
        for filename in files:
            Filelist.append(os.path.join(home, filename))
    return Dirlist, Filelist
```

```python
【1】os.path
os.path.abspath(path) #返回绝对路径（__file__当前文件）
os.path.basename(path) #返回文件名，去除路径
os.path.commonprefix(list) #返回list(多个路径)中，所有path共有的最长的路径。
os.path.dirname(path) #返回文件路径
os.path.exists(path)  #路径存在则返回True,路径损坏返回False
os.path.lexists  #路径存在则返回True,路径损坏也返回True
os.path.expanduser(path)  #把path中包含的"~"和"~user"转换成用户目录
os.path.expandvars(path)  #根据环境变量的值替换path中包含的”$name”和”${name}”
os.path.getatime(path)  #返回最后一次进入此path的时间。
os.path.getmtime(path)  #返回在此path下最后一次修改的时间。
os.path.getctime(path)  #返回path的大小
os.path.getsize(path)  #返回文件大小，如果文件不存在就返回错误
os.path.isabs(path)  #判断是否为绝对路径
os.path.isfile(path)  #判断路径是否为文件
os.path.isdir(path)  #判断路径是否为目录
os.path.islink(path)  #判断路径是否为链接
os.path.ismount(path)  #判断路径是否为挂载点（）
os.path.join(path1[, path2[, ...]])  #把目录和文件名合成一个路径
os.path.normcase(path)  #转换path的大小写和斜杠
os.path.normpath(path)  #规范path字符串形式
os.path.realpath(path)  #返回path的真实路径
os.path.relpath(path[, start])  #从start开始计算相对路径
os.path.samefile(path1, path2)  #判断目录或文件是否相同
os.path.sameopenfile(fp1, fp2)  #判断fp1和fp2是否指向同一文件
os.path.samestat(stat1, stat2)  #判断stat tuple stat1和stat2是否指向同一个文件
os.path.split(path)  #把路径分割成dirname和basename，返回一个元组
os.path.splitdrive(path)   #一般用在windows下，返回驱动器名和路径组成的元组
os.path.splitext(path)  #分割路径，返回路径名和文件扩展名的元组
os.path.splitunc(path)  #把路径分割为加载点与文件
os.path.walk(path, visit, arg)  #遍历path，进入每个目录都调用visit函数，visit函数必须有
3个参数(arg, dirname, names)，dirname表示当前目录的目录名，names代表当前目录下的所有
文件名，args则为walk的第三个参数
os.path.supports_unicode_filenames  #设置是否支持unicode路径名
【2】
os.mkdir(path)不会创建，
os.makedirs(path)则会创建父目录。
```

## 15. pathlib

## 16. pywifi

## 17. queue

1. `线程安全`的队列（FIFO）实现,提供了一个适用于多线程编程的先进先出的数据结构，即队列，用来在生产者和消费者线程之间的信息传递.
2. `empty()`判断是否为空。

### 17.1. class 类

#### 17.1.1. Queue

1. 先进先出。

#### 17.1.2. LifoQueue(Queue)

1. 先进后出。

#### 17.1.3. PriorityQueue(Queue)

1. 优先级队列

## 18. random

## 19. re 文本处理

```python
re_telephone = re.compile(r'^(\d{3})-(\d{3,8})$')  # 预编译（正则字符串） r表示不转义
A = re_telephone.match('010-12345').groups()  # 使用
print(A)  # 结果 ('010', '12345')
A = re_telephone.match('010-12345').group(1)  # 使用group,指定第几个
A = re_telephone.search('010-12345')    # search一般是搜索，比match宽松，match必须是完全匹配字符串
B = re_telephone.match('010-8086').groups()  # 使用
print(B)  # 结果 ('010', '8086')
re.findall()    # 可以匹配多次
re.sub([正则],[替换为],[元字符串])    #替换，替换为空即删除 
```

### 19.1. 元字符（常用）

```python
.    #匹配除换行符 \n 之外的任何单字符
^    #以什么开头
$    #以什么结尾
*    #匹配前面的子表达式零次或多次
+    #匹配前面的子表达式一次或多次
?    #匹配前面的子表达式零次或一次,或指明一个非贪婪限定符
{}   #指明前面的子表达式出现的次数
[]   #给定一个匹配池[a-z]
\d   #匹配数字
\D   #匹配非数字
\s   #匹配任何空白字符
\S   #匹配任何非空白字符
()   #分组
```

## 20. SHUTIL 文件和文件夹操作

| 代码              | 功能                    | 备注               |
| ----------------- | ----------------------- | ------------------ |
| `shutil.copytree` | 复制整个目录树（cp -r） | 不能覆盖目标文件夹 |
| `shutil.copyfile` | 复制文件                | 可以覆盖目标文件   |

## 21. socket

## 22. struct

1. 按照指定格式将Python数据转换为字符串,该字符串为字节流,如网络传输时,不能传输int,此时先将int转化为字节流,然后再发送;
2. 按照指定格式将字节流转换为Python指定的数据类型;
3. 处理二进制数据,如果用struct来处理文件的话,需要用’wb’,’rb’以二进制(字节流)写,读的方式来处理文件;
4. 处理c语言中的结构体;

### 22.1. 格式字符串

1. 格式字符串的第一个字符可用于指示打包数据的字节顺序、大小和对齐方式。

| Character | Byte order    | Size | Alignment       |
| --------- | ------------- | ---- | --------------- |
| @(默认)   | 本机          | 本机 | 本机,凑够4字节  |
| =         | 本机          | 标准 | none,按原字节数 |
| <         | 小端          | 标准 | none,按原字节数 |
| >         | 大端          | 标准 | none,按原字节数 |
| !         | network(大端) | 标准 | none,按原字节数 |

2. 格式符

| 格式符     | C语言类型          | Python类型         | Standard size |
| ---------- | ------------------ | ------------------ | ------------- |
| x          | pad byte(填充字节) | no value           |
| c          | char               | string of length 1 | 1             |
| b          | signed char        | integer            | 1             |
| B          | unsigned char      | integer            | 1             |
| ?          | _Bool              | bool               | 1             |
| h          | short              | integer            | 2             |
| H          | unsigned short     | integer            | 2             |
| i          | int                | integer            | 4             |
| I(大写的i) | unsigned int       | integer            | 4             |
| l(小写的L) | long               | integer            | 4             |
| L          | unsigned long      | long               | 4             |
| q          | long long          | long               | 8             |
| Q          | unsigned long long | long               | 8             |
| f          | float              | float              | 4             |
| d          | double             | float              | 8             |
| s          | char[]             | string             |
| p          | char[]             | string             |
| P          | void *             | long               |

### 22.2. def 方法

#### 22.2.1. `unpack`

1. 格式的长度必须和data的长度相等。

## 23. sys 操作系统交互

## 24. tarfile

## 25. time

## 26. timeit

## 27. threading 见并发编程

## 28. unitest

## 29. urllib

## 30. venv

## 31. xml
