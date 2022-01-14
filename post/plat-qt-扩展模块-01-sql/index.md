# QT-SQL


QtSql模块提供平台独立的基于SQL的数据库操作。这里的“平台独立”，既包括操作系统平台，也包括各个数据库平台。

```C++
#include <QSqlDatabase>
#include <QSqlQuery>
QT += sql 
```

## 1. QSqlDatabase连接

1. 一个实例是一个连接。

```c++
// 静态方法
contains()
database()//实例化一个连接
// 实例方法
setDatabaseName()//设置数据库路径
open()//打开连接
driver() //获取数据库驱动用来操作数据库连接
close()//断开连接【注意，必须显式地调用，不要放在析构函数中，因为他不会立即调用】
```

## 2. QSqlDriver访问sql数据库的抽象基类

```c++
//实例方法
hasFeature()  //是否具有指定的特性 QSqlDriver::Transactions 支持事务
```

## 3. QSqlQuery 执行SQL语句

1. 操作SQL语句返回的结果集。
2. 在SQL语句中使用变量。
3. 批处理操作。
4. 事务操作。

```C++
//实例方法
exec()//执行sql语句
//批量导入库
prepare()//query.prepare("INSERT INTO students (name, score,class) VALUES (:name, :score, :class)");为每一列标题添加绑定值
bindValue()//query.bindValue(":name", name);然后执行exec()
//查询执行select之后
bool isSelect() const//判断当前的query是不是select语句
int size() const//返回select语句执行之后获得行的个数 -1数据库不支持
seek(int n)//query指向结果集的第n条记录；
first()//query指向结果集的第一条记录；
last()//query指向结果集的最后一条记录；
next()//指向下一条记录，每执行一次该函数，便指向相邻的下一条记录
previous()//query指向上一条记录，每执行一次该函数，便指向相邻的上一条记录
QSqlRecord record()//获得现在指向的记录；
record().indexOf("country")//从查询结果中获取字段的索引
QVariant value()//根据索引读取当前行中的一个列值
at()//获得现在query指向的记录在结果集中的编号。
int numRowsAffected() const//返回执行Noselect语句之后，受影响的行的个数
```

## 4. QSqlRecord 一条记录

```c++
//实例方法
QVariant value(int index)// 获取索引的属性值
```
