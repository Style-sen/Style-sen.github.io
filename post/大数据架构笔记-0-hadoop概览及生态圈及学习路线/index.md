# 大数据架构笔记-hadoop生态圈-学习路线



# 求职

1. 简历要好好准备；
2. 准备面试：先讲框架，再谈细节。优化细节，框架演化。

# hadoop生态圈

1. 从上往下，从底层往上层。
2. 所有需要通过webserver提供服务。

## hadoop

### 3.0 新特性
1. Classpath isolation:增加此配置，防止不同版本jar包冲突。
2. Shell重写了。
3. 支持HDFS中的擦除编码（Erasure Encoding）默认的EC策略可以节省50%的存储空间，同时还可以承受更多的存储故障。
4. DataNode内部添加了负载均衡（Disk Balancer）。
5. MapReduce任务级本地优化。
6. MapReduce内存参数自动推断。
7. 基于cgroup的内存隔离和IO Disk隔离。
8. 支持更改分配容器的资源Container resizing（内存和磁盘）。

### 3.0默认端口发生变化（）

### Hadoop Common

### HDFS（分布式文件系统） 最底层  【存储】

### YARN

### MapReduce 【计算框架】
1. 适用于离线的批量的数据计算。

## 其他相关组件（独立项目）

### Flume【日志收集工具】
1. Flume 是将数据从产生、传输、处理并最终写入目标路径的过程抽象为数据流，在具体的数据流中，数据源支持在 Flume 中定制数据发送方，从而支持收集各种不同协议数据。
2. Flume 数据流提供对日志数据进行简单处理的能力，如过滤、格式转换等。
3. Flume 还具有能够将日志写往各种数据目标（文件、HDFS、网络）的能力。在 Hadoop 平台，我们主要使用的是通过 Flume 将数据从源服务器写入 Hadoop 的 HDFS 上。

### Hbase（分布式数据库）【存储】
1. 基于HDFS，数据还是存在于HDFS。
2. 列存储。
2. 最大优点是查询速度快，这对数据完整性要求不高的大数据处理领域，比如互联网，犹为重要。
4. 非常适合需对数据进行随机读、写操作、比如每秒对PB级数据进行几千次读、写访问是非常简单的操作。 其次，Hbase构建在HDFS之上，其内部管理的文件全部存储在HDFS中。这使它具有高度容错性和可扩展性，并支持Hadoop mapreduce程序设计模型。
5. 如果你的应用是`交易历史查询系统`、查询场景简单，检索条件较少、每天有`千万行数据更新`、那么Hbase将是一个很好的选择。

### Hive【SQL引擎】
1. 本质是mapreduce。
2. 将SQL转换到MapReduce上执行。
3. 哪怕你不熟悉 MapReduce 程序，只要会写标准的 SQL 语句，也能对 HDFS 上的海量数据进行分析和计算。`通常做统计`。

### Kafka【分布式消息队列】
1. 最大特性就是可以实时的处理大量数据以满足各种需求场景：比如基于 Hadoop 平台的数据分析、低时延的实时系统、Storm/Spark 流式处理引擎等。
2. Kafka 现在它已被多家大型公司作为多种类型的数据管道和消息系统使用。

### mahout【机器学习】


### Oozie【工作流调度器】
1. Oozie 是一个基于工作流引擎的调度器，它其实就是一个运行在 Java Servlet 容器（如 Tomcat）中的 Javas Web 应用，你可以在它上面运行 Hadoop 的 Map Reduce 和 Pig 等任务，。
2. 对于 Oozie 来说，工作流就是一系列的操作（如 Hadoop 的 MR，Pig 的任务、Shell 任务等），通过 Oozie 可以实现多个任务的依赖性。也就是说，一个操作的输入依赖于前一个任务的输出，只有前一个操作完全完成后，才能开始第二个。
3. Oozie 工作流通过 hPDL 定义（hPDL 是一种 XML 的流程定义语言），工作流操作通过远程系统启动任务。当任务完成后，远程系统会进行回调来通知任务已经结束，然后再开始下一个操作。

### pig
1. Hive 可以把脚本和 SQL 语言翻译成 MapReduce 程序，扔给计算引擎去计算。Pig 与 Hive 类似，它定义了一种数据流语言，即 Pig Latin，它是 `MapReduce 编程的复杂性的抽象`.
2. Pig Latin 可以完成排序、过滤、求和、关联等操作，支持自定义函数。Pig 自动把 Pig Latin 映射为 MapReduce 作业，上传到集群运行，减少用户编写 Java 程序的苦恼。

### Spark【计算框架】
1. Spark 提供了内存中的分布式计算能力，相比传统的 MapReduce 大数据分析效率更高、运行速度更快。总结一句话：以内存换效率。
1. 批量计算，spark core
2. 实时计算， stream
3. 还可以机器学习， mllib
4. 还可以SQL

### Sqoop
1. 通过 Sqoop（SQL-to-Hadoop）主要用于传统数据库和 Hadoop 之间传输数据。数据的导入和导出本质上是 MapreDuce 程序，充分利用了 MR 的并行化和容错性。

### Storm【计算框架】【渐渐过时】
1. 实时计算。

### zookeeper 集群管理，分布式锁服务
1. ZooKeeper 相当于一个和事佬的角色，如果两人之间发生了一些矛盾或者冲突，无法自行解决的话，这个时候就需要 ZooKeeper 这个和事佬从中进行调解，而和事佬调解的方式是站在第三方客观的角度，根据一些规则（如道德规则、法律规则），客观的对冲突双方做出合理、合规的判决。
2. 节点丢失或重启，都会发起`选举`。

# 常见业务

## 搜索
1. 搜索内容；
2. 提供服务。

## 广告

## 推荐

# 职业方向

# 学习路线
1. [雨雀文档](https://www.yuque.com/sunzanfeng/weovci/hy3i5c)

## Java SE核心知识（第一阶段）

### Java编程概述及基本语法
1. Java技术体系平台
2. Java核心机制与JVM运行原理
3. Java开发环境搭建
4. Java变量、数据类型、数组及运算符
5. Java流程控制结构
6. 面向对象编程
7. 高级类特性
8. Java API、异常机制、反射及IO流
9. Lambda表达式
10. 函数式接口

###Java集合与泛型
集合框架概述
Collection 系列集合
List系列集合与Set系列集合
Iterator 与 ListIterator
Collections 工具类
集合中使用泛型
自定义泛型

### Java多线程技术
线程的原理
线程的创建与启动
创建线程的几种方式对比
继承 Thread 类与实现 Runnable 接口
创建线程方式对比
线程的控制
线程的调度
线程的优先级
线程的生命周期
多线程的安全问题与解决办法
线程的同步
互斥锁
线程的死锁问题
线程通信
生产者与消费者案例

### Java SE网路编程
网络编程基础知识
网络编程的主要问题
如何实现网络中主机的相互通讯
网络通讯要素
网络通信协议
OSI参考模型
TCP/IP参考模型(或TCP/IP协议)
数据的封装与拆封
Java.net.InetAddress 类
TCP 协议与UDP协议
基于TCP协议的网络编程
Socket 的TCP编程
基于UDP协议的网络编程
URL编程
针对HTTP协议的URLConnection

## Java Web数据可视化（第二阶段）

### Java Web 后端技术
Tomcat&Nginx服务器中间件
Servlet规范组件
Ajax&Json轻量级数据交互
Maven项目构建工具
Preformance 工具的使用及注意事项
Git项目协作工具
Spring依赖管理框架
SpringMVC表现层框架
Mybatis持久层框架
分布式架构理论

### 前端可视化技术
XML可扩展标记语言
HTML、CSS、JavaScript静态化技术
Jquery、Vue脚本框架
ECharts可视化组件
FusionCharts可视化组件

### 关系型数据库MySQL高级应用
MySQL基础
MySQL数据库对象
SQL结构化查询语言
JDBC操作
MySQL查询和慢查询日志分析
MySQL调优之索引优化
MySQL调优之存储引擎优化
MySQL调优之锁机制优化
MySQL高可用

### Linux 服务器应用实战
Linux系统基础
Linux网络基础
Linux安全策略
Shell编程基础及应用
Linux运维技巧

### 拉勾网招聘行业报表数据可视化项目实战

## Hadoop核心及生态圈技术栈（第三阶段）

### Hadoop核心之分布式文件系统HDFS
大数据概述
Hadoop概述
Apache Hadoop完全分布式集群部署
HDFS简介及应用场景
HDFS原理详解
HDFS Shell 操作
HDFS Java API 操作
HDFS原理输入及调优
双缓冲机制
HDFS源码剖析、设计模式及二次开发

### Hadoop核心之分布式计算框架MapReduce
MapReduce设计目标及核心思想
MapReduce主要功能
MapReduce处理流程
MapReduce入门案例
Mapper抽象类与Reducer抽象类
MapReduce内置数据类型及自定义数据类型
如何确定Map和Reduce个数
MapReduce Shuffle过程之Combine合并机制
MapReduce Shuffle过程之Partition分区机制、自定义分区
MapReduce Shuffle之序列化
MapReduce Shuffle过程之自定义排序及二次排序
MapReduce Shuffle过程之数据压缩机制
自定义InputFormat及OutputFormat
MapReduce生产级调优
MapReduce案例实战

### Hadoop核心之分布式资源调度框架Yarn
分布式资源调度及Yarn简介
Yarn架构及原理
Yarn核心组件之ResourceManager 资源管理器
Yarn核心组件之NodeManager 节点管理器
Yarn核心组件之Application Master 任务专员
Yarn核心组件之Container 容器
Yarn资源调度FIFO策略和应用
Yarn资源调度Fair策略和应用
Yarn资源调度Capacity策略和应用
Yarn多租户资源调度配置
Yarn生产级调优

### Hadoop生态圈技术栈（上）
数据仓库Hive高级应用
ETL转换抽取工具Sqoop
ETL转换抽取工具CDC
ETL转换抽取工具DataX
数据采集工具Flume
分布式协调服务组件ZooKeeper
海量列式非关系型数据库HBase

### Hadoop生态圈技术栈（下）
海量列式非关系型数据库HBase
SQL语义级查询系统Impala
任务调度组件Oozie
任务调度组件Azkaban
任务调度组件airflow

## 分布式缓存Redis及Kafka消息中间件（第四阶段）

### 高性能分布式缓存Redis
NoSQL&Redis入门
分布式数据库CAP原理
Redis五大数据类型和基本操作命令
Redis总体配置Redis.conf
Redis持久化（RDB和AOF）
Redis事务控制
Redis发布和订阅
Redis（Master/Slave）主从复制
Java客户端Jedis
通讯协议及事件处理机制
Redis + Lua环境协作组件
Redis慢查询及监视器

### 高吞吐消息中间件Kafka
Kafka架构
Kafka安装及高级应用
Kafka生产者原理
Kafka broker端原理
Kafka 集群
Kafka消费者源码剖析
Kafka深度调优
Flume+Kafka整合应用

## PB级电商离线数仓项目实战（第五阶段）
[大数据数据仓库实战项目-电商数仓教程V3.0最新版](https://www.bilibili.com/video/BV1dD4y1d7wL)
1. 数仓分层建设及理论梳理
2. 数仓建设痛点剖析
3. 数据血缘管理机制
4. 质量监控策略
5. 离线数仓全流程性能优化方案
6. 电商行业离线数仓业务真实级源码构建与剖析

## 内存级快速计算引擎Spark（第六阶段）

### Scala编程
Scala基础
Scala控制结构和函数
Scala数组相关操作
Scala映射和元组
Scala继承、特质、高阶函数
Scala集合
Scala模式匹配和样例类
Scala偏函数
Scala Future及类型参数
Scala 高级类型
Scala 隐式转换和隐式参数

### Spark实战应用
Spark Core核心
Spark SQL结构化处理
Spark Streaming流处理
Spark GraphX图挖掘
Spark MLib机器学习
Spark综合案例实战

## 智慧物流项目（第七阶段）

### 数据采集
Flume/Maxwell

### 仓库缺货-销量预测
LigthGBM模型算法

### 运输车辆调度
动态规划算法

### 车辆位置/指标实时监控
实时指标Redis存储
SparkStreaming消费一致性，Exactly Once解决方案

### 轨迹回放
HBase海量历史数据存储
HBase应用及优化
### 多维度业务指标看板

## 实时计算领域最锋利的武器Flink（第八阶段）

### Flink基础
Flink 的应用场景和架构模型
Flink 入门程序 WordCount 和 SQL 实现
Flink 的编程模型与其他框架比较
Flink 常用的 DataSet 和 DataStream API
Flink SQL & Table 编程和案例
Flink 集群安装部署和 HA 配置

### Flink进阶
Flink 常见核心概念分析
Flink 窗口、时间和水印
Flink 状态与容错
Flink Side OutPut 分流
Flink CEP 复杂事件处理
Flink 常用的 Source 和 Connector

### Flink高级实践及调优
Flink 高可用配置
Flink Exactly-once 实现原理解析
Flink反压问题排查
Flink数据倾斜问题处理
Flink并行度和资源设置
Flink生产环境作业监控
Flink维表关联方案
Flink海量数据高效去重
Flink与Kafka的整合
Flink 中 watermark 的定义和使用
Flink 中的聚合函数和累加器的设计和使用
自定义消息事件及Pattern

### Flink综合案例实战

## 大数据新技术实践（第九阶段）

### OLAP列式数据库管理系统ClickHouse
ClickHouse简介及应用场景
ClickHouse架构及原理
ClickHouse数据类型
ClickHouse列设计
ClickHouse物化列&表达式列
ClickHouse实战案例

### 新型列式存储分布式数据库Kudu
Kudu 简介及应用场景
Kudu 的高层设计
Kudu 中的角色
Kudu 中的概念与机制
Kudu 安装部署
Kudu 管理
Kudu操作接口
Kudu性能调优
Kudu实战案例
### 开源的分布式分析引擎Kylin
Kylin的工作原理
Kylin的技术架构
Kylin的主要特点
Kylin快速入门
Cube优化
增量构建
查询和可视化
Cube Planner及仪表盘
流式构建
Kylin实战案例

### 实时统计分析开源数据存储Druid
Druid原理及部署
Druid数据摄入
Druid客户端
Druid实战案例

## Elastic Stack 日志搜索、挖掘及可视化解决方案（第十阶段）

### Elasticsearch全文搜索引擎
认识全文搜索引擎
倒排索引机制
全文搜索引擎Elasticsearch介绍
Elasticsearch Single-Node Mode快速部署
Elasticsearch之入门使用
Elasticsearch之高级应用
Elasticsearch之企业级高可用分布式集群
Elasticsearch之数据模型构建
Elasticsearch之拉勾网亿级数据量搜索实战
Elasticsearch之深度应用及原理剖析
Elasticsearch 7.x分布式集群调优策略

### Logstash采集、Kibana展示方案
Intput插件应用及原理
自定义Intput插件
Filter插件
自定义Filter过滤插件
Output插件应用及原理
自定义Output插件
Kibana可视化组件

## 电商行业实时数仓项目（第十一阶段）
Flink流式处理技术构建复杂的电商订单指标
ClickHouse快速电商业务查询
数据完整处理链条展现
电商大促峰值数据大数据解决方案

## Hadoop/Spark大数据处理算法及案例（第十二阶段）

### 大数据处理算法及案例（上）
K-均值算法
KNN算法
朴素贝叶斯
马尔可夫链
推荐算法
成对文档相似性
算法案例实战应用

### 大数据处理算法及案例（下）
线性回归
Cox回归
皮尔逊（Pearson）相关分析
社交网络分析之推荐系统
社交网络分析之三角形计数
社交网络分析之情感分析
算法案例实战应用

## 机器学习（第十三阶段）

### Python 编程核心
交互式解释器
Python基础语法
Python面向对象
Python数据结构
Anaconda工具使用

### Python编程实战
PySpark
Python与SparkSql整合
Python与SparkStreaming整合
Python+SparkMllib机器学习实战项目

## 人才用户画像匹配系统（第十四阶段）
用户建模确定特征维度
标签分类及标签库设计
ETL计算
特征处理
推荐算法模型处理，人才和企业精准对接




