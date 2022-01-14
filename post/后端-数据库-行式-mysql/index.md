# 后端-数据库-MYSQL


[MYSQL基本教程](https://www.runoob.com/mysql/mysql-tutorial.html)

## 面试必备（原理）
1. 数据库解决`全量IO`的问题。

### 事务隔离
1. 在数据库系统中，事务隔离级别(isolation level)决定了数据在系统中的可见性。隔离级别从低到高分为四种：未提交读(Read uncommitted)，已提交读(Read committed)，可重复读(Repeatable read)，可串行化(Serializable)。他们的区别如下表所示。

|隔离级别|脏读|不可重复读|幻读|
|--|--|--|--|
|未提交读(RU)|可能|可能|可能|
|已提交读(RC)|不可能|可能|可能|
|可重复读(RR)|不可能|不可能|可能|
|可串行化|不可能|不可能|不可能|

2. 脏读(dirty read)是指一个事务可以读到其他事务还未提交的数据。不可重复读(non-repeatable read)是指在一个事务中同一行被读取了多次，可以读到不同的值。幻读(phantom read)是指在一个事务中执行同一个语句多次，读到的数据行发生了改变，即可能行数增加了或减少了。对于MySQL来说，默认的事务隔离级别是RR，通过上表我们可知RR是可重复读的，因此可以解释这个现象。

### 索引
1. 数据存在磁盘上的data page（默认4K）中。（分治）
2. 建表之前创建schema，指定字段类型（实际上是空间），是为了确定表中每一行的长度。方便查找。
3. MySQL索引的建立对于MySQL的高效运行是很重要的，索引可以大大提高MySQL的检索速度。
4. 建立索引的字段`必须是`数据表中最常用来查询（where）的字段。
5. 索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。组合索引，即一个索引包含多个列。
6. 创建索引时，你需要确保该索引是应用在 SQL 查询语句的条件(一般作为 WHERE 子句的条件)。
7. 实际上，`索引也是一张表`，该表保存了主键与索引字段，并指向实体表的记录。
8. `缺点`过多的使用索引将会造成滥用。因此索引也会有它的缺点：虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。建立索引会占用磁盘空间的索引文件。
9. 索引也是数据，也需要查询，大了之后也需要提高速度。这个时候有一个`B+`数。树干在内存，树叶在磁盘。
10. `因为有了索引`，数据表变大了，查询不一定会变慢。`WHERE`只有索引，是不会慢的。但是并发量比较大，查询比较复杂，速度还是比较慢的（磁盘IO影响）。

## MYSQL配置及优化

配置项 | 描述 | 默认值
--|-- | --
wait_timeout|wait_timeout过大有弊端，其体现就是MySQL里大量的SLEEP进程无法及时释放，拖累系统性能，不过也不能把这个指设置的过小，否则你可 能会遭遇到“MySQL has gone away”之类的问题 |
max_connections | 最大连接数.并法高，调高此值；但是会消耗内存，合适的值（SHOW STATUS LIKE 'max%connections';max_used_connections / max_connections * 100% （理想值≈85%）） | 151
back_log | 如果MySQL的连接数据达到max_connections时，新来的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈的数量即back_log | 默认值是80，最多512，可设置为128

## MYSQL优化 

## 数据库云

### RDS(Aliyun)
1. [RDS for MySQL 物理备份文件恢复到自建数据库](https://help.aliyun.com/knowledge_detail/41817.html);
2. [数据同步方案概览](https://help.aliyun.com/document_detail/157518.html)
3. [跨阿里云账号迁移RDS实例](https://help.aliyun.com/document_detail/26652.html?spm=a2c4g.11186623.2.56.230251c1ONvmDR#concept-xng-n1b-mhb)
```
如需执行增量数据迁移(或者数据同步)，需开启Binlog并满足以下要求：
binlog_format设置为row。
binlog_row_image设置为full。
Binlog日志应至少保留24小时（建议3天以上）。
```

## SQL语句
1. mysql的创建表的语句后面有auto_increment=4.AUTO_INCREMENT=n命令来重设自增的起始值。但是如果设置的n比目前的数值小的话，执行的sql不会报错，但是不会生效！

|||
|--|--|
|<>|不相等|
|in|在区间|

### 常用操作
1. 删除字段：`ALTER TABLE table_NAME DROP COLUMN column_NAM`

### 联查`JOIN`
1. SQL join 用于根据两个或多个表中的列之间的关系，从这些表中查询数据。
2. 普通的用法：`SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo FROM Persons, Orders WHERE Persons.Id_P = Orders.Id_P `。
3. `默认`使用`inner join`，INNER JOIN 关键字在表中存在至少一个匹配时返回行。如果 "Persons" 中的行在 "Orders" 中没有匹配，就不会列出这些行。
```
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
INNER JOIN Orders
ON Persons.Id_P = Orders.Id_P
ORDER BY Persons.LastName
```
4. 使用`left join`, LEFT JOIN 关键字会从左表 (Persons) 那里返回所有的行，即使在右表 (Orders) 中没有匹配的行。
5. 使用`right join`, RIGHT JOIN 关键字会从右表 (Orders) 那里返回所有的行，即使在左表 (Persons) 中没有匹配的行。
6. 受用`full join`,FULL JOIN 关键字会从左表 (Persons) 和右表 (Orders) 那里返回所有的行。如果 "Persons" 中的行在表 "Orders" 中没有匹配，或者如果 "Orders" 中的行在表"Persons" 中没有匹配，这些行同样会列出。

### 模糊查询
1. `SELECT 字段 FROM 表 WHERE 某字段 Like 条件`

#### 条件
1. `%`表示任意0个或多个字符。可匹配任意类型和长度的字符，有些情况下若是中文，请使用两个百分号（%%）表示。
2.  `_` 表示任意单个字符。匹配单个任意字符，它常用来限制表达式的字符长度语句。
3. `[ ]` 表示括号内所列字符中的一个（类似正则表达式）。指定一个字符、字符串或范围（使用-），要求所匹配对象为它们中的任一个。
4. `[^ ]` 表示不在括号所列之内的单个字符。其取值和 `[]` 相同，但它要求所匹配对象为指定字符以外的任一个字符。
5. 由于通配符的缘故，导致我们查询特殊字符"%"、"_"、"["的语句无法正常实现，而把特殊字符用"[ ]"括起便可正常查询。

### HAVING子句 
1. 在SELECT语句中使用HAVING子句来指定`一组行或聚合`的过滤条件。
2. `HAVING子句通常与GROUP BY子句一起使用，放在其后`，以根据指定的条件过滤分组之后的结果。如果省略GROUP BY子句，则HAVING子句的行为与WHERE子句类似。
3. 请注意，HAVING子句将过滤条件应用于每组分行，而WHERE子句将过滤条件应用于每个单独的行。

## 设计规范
1. 参考[MySQL数据库设计规范](https://gitee.com/ilanni/archer/blob/master/src/docs/mysql_db_design_guide.md)

### 表结构
8. `建议`表中所有字段必须都是NOT NULL属性，业务可以根据需要定义DEFAULT值。因为使用NULL值会存在每一行都会占用额外存储空间、数据迁移容易出错、聚合函数计算结果偏差等问题。
10. `建议`反范式设计：把经常需要join查询的字段，在其他表里冗余一份。如user_name属性在user_account，user_login_log等表里冗余一份，减少join查询。

### 线上禁止使用的SQL语句
3. `强制`禁用procedure、function、trigger、views、event、`外键约束`。因为他们消耗数据库资源，降低数据库实例可扩展性。推荐都在程序端实现。

## [官方docker镜像](https://hub.docker.com/_/mysql/)

### 配置

1. 默认的配置文件`/etc/mysql/my.cnf`。

#### 自定义配置文件

#### 启动参数

1. 可以使用`command`指定启动参数。
```
version: '3.1'

services:

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    ports:
      - '3306:3306'
    environment:
      MYSQL_ROOT_PASSWORD: example
    volumes:
      - ./db_data:/var/lib/mysql:Z

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

#### 环境变量

1. `注意`，如果用一个已经包含数据库的数据目录启动容器，那么下面的变量都不会有任何影响:在容器启动时，任何预先存在的数据库都不会改变。
```
MYSQL_ROOT_PASSWORD    强制性的，设置root超级用户的密码
MYSQL_DATABASE    可选的，指定镜像启动时，创建的数据库名称
MYSQL_USER, MYSQL_PASSWORD    可选的，用于创建上述数据库的用户和密码
MYSQL_ALLOW_EMPTY_PASSWORD    可选的（yes），允许可以不设置root密码，不建议，不安全
MYSQL_RANDOM_ROOT_PASSWORD    可选的（yes），设置root一个随机密码，会在stdout打印 (GENERATED ROOT PASSWORD: .....).
MYSQL_ONETIME_PASSWORD    设置root一次性密码，登录之后必须修改
MYSQL_INITDB_SKIP_TZINFO    任何非空值，禁用时区加载
```

### 存储
1. 默认存储路径`/var/lib/mysql`.

### 首次启动
1. 首次启动容器时，将创建一个具有指定名称的新数据库，并使用提供的配置变量对其进行初始化。 
2. 此外，它将执行在`/docker-entrypoint-initdb.d`中找到的扩展名为`.sh，.sql和.sql.gz`的文件。 文件将按字母顺序执行。 
3. You can easily populate your mysql services by mounting a SQL dump into that directory and provide custom images with contributed data.
4. 默认情况下，SQL文件将导入到MYSQL_DATABASE变量指定的数据库中。

## 答疑解惑
1. [删除数据的时候可能会出现MYSQL SAFE UPDATE MODE](https://blog.csdn.net/anan890624/article/details/50991404)；
2. [1273 - Unknown collation: 'utf8mb4_0900_ai_ci'](https://github.com/gavenwangcn/vole/issues/4);
3. [RDS MySQL出现“OPERATION need to be executed set by ADMIN”报错](https://help.aliyun.com/knowledge_detail/41701.html?spm=a2c4g.11186631.2.2.64d33641KBWxTO)
4. 可以使用`mysql workbench`来导出和导入数据,注意需要删除`AUTO_INCREMENT=321`属性。
5. [解决MySQL报错：[Err] 1055 - Expression #1 of ORDER BY clause is not in GROUP BY clause and contains no...](https://www.jianshu.com/p/c09df83c9805)
6. [微信昵称emoji表情](https://help.aliyun.com/knowledge_detail/41702.html)
```
原因：MySQL的utf8编码方式的数据为三个字节。MySQL的utf8编码改为utf8mb4编码方式。
解决方法：
1. 查看数据库是否支持，SHOW CHAR SET WHERE Charset LIKE "%utf8%";
2. 修改数据库支持：ALTER DATABASE [$Database] CHARACTER SET [$Character_Set] COLLATE [$Collation_Name];
3. 修改表支持：ALTER TABLE [$Table] CONVERT TO CHARACTER SET [$Character_Set] COLLATE [$Collation_Name];
4. 重启：
```
