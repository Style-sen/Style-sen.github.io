# Sqlite


参考[主键](https://www.liaoxuefeng.com/wiki/1177760294764384/1218728391867808)
参考[易百教程](https://www.yiibai.com/sqlite/data-types.html)
参考[CSDN](https://blog.csdn.net/qq_35844043/article/details/97634257)

## 1. 概念

### 1.1. 数据类型

1. SQLite使用更通用的动态类型系统。 在SQLite中，值的数据类型与值本身相关联，而不是与其容器相关联。
2. 在SQLite中，没有一个单独的布尔存储类。一个代替办法是将布尔值存储为整数0(假)和1(真)。
3. 在SQLite中，没有单独的类型来存储日期和时间。 但是可以将日期和时间存储为TEXT，REAL或INTEGER值.

#### 1.1.1. 存储类

1. SQLite存储类比数据类型更通用一些。 例如：INTEGER存储类包括不同长度的6种不同的整数数据类型。
2. SQLite数据库中存储的值是以下存储类之一：

| 存储类  | 描述                                                                  |
| ------- | --------------------------------------------------------------------- |
| NULL    | 表示值为空(null)值。                                                  |
| INTEGER | 表示值是一个有符号整数，根据值的大小存储在1,2,3,4,6或8个字节中。      |
| REAL    | 表示值是一个浮点值，存储为8位IEEE浮点数。                             |
| TEXT    | 表示值是一个文本字符串，使用数据库编码(utf-8，utf-16be或utf-16le)存储 |
| BLOB    | 表示值是一个数据块，与输入的数据完全相同，二进制                      |

#### 1.1.2. 近似类型

1. SQLite支持列的类型近似性。`列可以存储任何类型的数据`，但是列的首选存储类称为它的近似性类型。

| 数据类型                                                                                                        | 相应的近似类型 |
| --------------------------------------------------------------------------------------------------------------- | -------------- |
| INT INTEGER TINYINT SMALLINT（16bit整数） MEDIUMINT BIGINT UNSIGNED BIG INT INT2 INT8                           | INTEGER        |
| VARCHAR(n长度不固定，最大，4000) VARYING CHARACTER(n) NCHAR(55) NATIVE NVARCHAR(100) TEXT CLOB CHAR(n不超过254) | TEXT           |
| BLOB - 未指定数据类型                                                                                           | NONE           |
| REAL DOUBLE DOUBLE PRECISION FLOAT DECIMAL(p,s)(精确值，小数位数)                                               | REAL           |
| NUMERIC DECIMAL(10,5) BOOLEAN DATE DATETIME                                                                     | NUMERIC        |

### 1.2. 主键

1. 记录一旦插入到表中，主键最好不要再修改，因为主键是用来唯一定位记录的，修改了主键，会造成一系列的影响。
2. 选取主键的一个基本原则是：【不使用任何业务相关的字段作为主键】。
3. 作为主键最好是完全业务无关的字段，我们一般把这个字段命名为`id`。常见的可作为id字段的类型有：
    a. 自增整数类型【最常用】：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；
    b. 全局唯一GUID类型：使用一种全局唯一的字符串作为主键，类似8f55d96b-8acc-4636-8cb8-76bf8abc2f57。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。
4. [联合主键]：允许一列有重复，只要不是所有主键列都重复即可。

### 1.3. AUTOINCREMENT

| 序号 | 字段定义                          | 是否可以自增 | 备注                                                                                                                                                           |
| ---- | --------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | INT PRIMARY KEY                   | 否           | 字段值默认为NULL                                                                                                                                               |
| 2    | INT PRIMARY KEY AUTOINCREMENT     | 否           |
| 3    | INTEGER PRIMARY KEY               | 是           | 自增（引擎没有创建sqlite_sequence表）。值 = 此表最大值 + 1，故可与之前删除的值重叠；当超过最大值时，会随机找一个没被使用的值                                   |
| 4    | INTEGER PRIMARY KEY AUTOINCREMENT | 是           | 自增(引擎自动在sqlite_sequence表中添加一条记录)，值 = sqlite_sequence表中对应值 + 1。故一般不会与之前删除的值重叠；当超过最大值时，会丢出一个SQLITE_FULL的错误 |

1. 自增的使用，必须满足以下两点:
    a. 只能用于整型（INTEGER）字段，INT类型是不可以；
    b. 只能用于PRIMARY KEY字段；
2. 然后，插入的SQL就是：`INSERT INTO Product VALUES(NULL, '產品名稱')`. 主键为NULL，就会自动自增填充。

#### 1.3.1. 自增主键清零

1. 当SQLite数据库中包含自增列时，会自动建立一个名为`sqlite_sequence`的表。这个表包含两个列：`name`和`seq`。`name`记录自增列所在的表，`seq`记录当前序号（下一条记录的编号就是当前序号加1）。
2. 如果想把某个自增列的序号归零，只需要修改`UPDATE sqlite_sequence SET seq = 0 WHERE name='TableName'`或者`DELETE FROM sqlite_sequence WHERE name='TableName'`;
3. 要想将所有表的自增列都归零，直接清空sqlite_sequence表就可以了`DELETE FROM sqlite_sequence`.

### 1.4. 索引

1. 简单地说，索引是一个指向表中数据的指针。
2. 索引有助于加快 SELECT 查询和 WHERE 子句，但它会减慢使用 UPDATE 和 INSERT 语句时的数据输入。

#### 1.4.1. 单列索引

```sql
CREATE INDEX index_name ON table_name (column_name);
```

#### 1.4.2. 唯一索引

```sql
CREATE UNIQUE INDEX index_name on table_name (column_name);
```

#### 1.4.3. 组合索引

```sql
CREATE INDEX index_name on table_name (column1, column2);
```

#### 1.4.4. 隐式索引

1. 隐式索引是在创建对象时，由数据库服务器自动创建的索引。索引自动创建为主键约束和唯一约束。

### 1.5. 触发器

1. SQLite 的触发器（Trigger）可以指定在特定的数据库表发生 DELETE、INSERT 或 UPDATE 时触发，或在一个或多个指定表的列发生更新时触发。
2. SQLite 只支持 FOR EACH ROW 触发器（Trigger）（`操作语句每影响到一行的时候就触发一次`），没有 FOR EACH STATEMENT 触发器（Trigger）。

```sql
-- 创建触发器
CREATE TRIGGER supplier_change_trigger 
AFTER INSERT -- 触发的操作（BEFORE|AFTER）（INSERT|UPDATE|DELETE）（OF column_name）指定哪一个字段
ON supplier_new -- 表名
BEGIN -- 到END之间是触发执行的语句
insert into supplier_data_change (info,updateTime)  
values (-- 记录供应商变化的时间和id
'新增供应商ID='||(select max(id) from supplier_new),
datetime());
END;
-- 删除触发器
DROP TRIGGER trigger_name;
```

## 2. 附加数据库

1. `ATTACH DATABASE file_name AS database_name`福建一个数据库。
2. `DETACH DATABASE database_name`解除附加一个数据库。

## 3. shell命令

1. `sqlite3 db文件`打开一个数据库文件,不存在则创建。
2. `sqlite3 test.db < sql文件`打开数据库文件，并执行sql文件中sql语句。

## 4. sqlite中命令

1. `.help` 显示帮助信息；
2. `.read sql文件`对打开的数据库文件执行sql文件中语句。
3. `.tables`列出数据库文件中的所有表。
4. `.output xxx.sql`指定到处的sql文件。
5. `.dump`导出。
6. `.exit`退出。

## 5. sqlite客户端工具

### 5.1. [sqlitestudio](https://github.com/pawelsalawa/sqlitestudio)

1. 开源，C++和Qt编写。

#### 5.1.1. 使用

1. 【注】多个进程可以同时打开同一个数据库。多个进程可以同时做一个SELECT。但是，`只有一个进程可以在任何时刻对数据库进行更改`。`所以，如果要多个进程同时更改一个数据库的话，最好是执行sql之前创建连接，执行结束之后就断开连接`。

## 6. C++调用

### 6.1. [SOCI/soci](https://github.com/SOCI/soci)

1. SOCI is a database access library for C++ that makes the illusion of embedding SQL queries in the regular C++ code, staying entirely within the Standard C++.

### 6.2. [SQLiteWrappers](http://www.naughter.com/sqlitewrappers.html)
