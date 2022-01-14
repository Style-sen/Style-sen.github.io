# 大数据架构笔记-1-hadoop安装配置命令


本文以3.2.1版本为主

参考[大数据运维实战](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=144#/detail/pc?id=3079)
参考[尚硅谷大数据资料]()

# 选择发行版

1. 对于初学入门的话，建议选择 Apache Hadoop 版本最好，因为它的社区活跃、文档、资料详实。
2. 企业环境：在国内大型互联网企业中，使用较多的是 CDH【收费】 或 HDP 发行版本，个人推荐采用 HDP 发行版本，原因是部署简单、性能稳定。

# 版本兼容选择

|组件|old|new|
|--|--|--|
|Hadoop        |    2.7.2    | 3.1.3 |
|Zookeeper     |    3.4.10   | 3.5.7 |
|MySQL         |    5.6.24   | 5.7.16|
|Hive          |    1.2.1    | 3.1.2 |
|Flume         |    1.7.0    | 1.9.0 |
|Kafka         |  0.11-0.2   | 2.11-2.4.1|
|Kafka Eagle   |    1.3.7    | 1.4.5|
|Azkaban       |    2.5.0    | 3.84.4|
|Spark         |    2.1.1    | 3.0.0 |
|Hbase         |    1.3.1    | 2.0.5 |
|Phoenix       |    4.14.1   | 5.0.0 |
|Sqoop         |    1.4.6    | 1.4.6 |
|Presto        |    0.189    | 0.189 |
|Kylin         |    2.5.1    | 3.0.1 |
|Atlas         |    0.8.4    | 2.0.0 |
|Ranger        |    2.0.0    | 2.0.0 |
|Solr          |    5.2.1    | 7.7.0 |

# hadoop搭建

1. 参考官方文档[开始集群](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html#Prepare_to_Start_the_Hadoop_Cluster)
1. 官方文档支持5种搭建方式,参考[hadoop 生态圈介绍](https://www.jianshu.com/p/c3a834e45ae3)

```
1. 单机模式：非分布式。默认模式。作为一个JAVA进程，调试有用。
2. 伪分布式操作模式：单节点。伪分布式。作为分开的JAVA进程。通常使用这种模式进行开发和调试工作。
3. 全分布式部署。
4. HA Cluster，即高可用集群模式
5. HA + Federation Cluster，即高可用联邦集群模式
```

## 伪分布式部署

1. `伪分布式安装 Hadoop 只需要一台机器，硬件配置最低为 4 核 CPU、8G 内存即可`

## 全分布式集群搭建

1. 参考[官方文档](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html)从若干节点到千级节点。
2. `官方文档并不涉及安全和高可用`。
3. 将硬件划分为功能。
4. 通常，集群中的一台机器被指定为`NameNode`(hadoop文件系统的管家)，而另一台机器被指定为`ResourceManager`(yarn的管家，主要管理任务的执行，例如MapReduce任务)。这些是主节点。
5. 其他服务(如Web App Proxy Server和MapReduce Job History Server)通常在专用硬件或共享基础设施上运行，这取决于负载。
6. 集群中的其他机器同时充当DataNode和NodeManager。这些是workers(从节点)。
7. 视业务、资金等情况而定，因为该模式日后也可以安全升级成高可用模式。

### 安装`java`
[Linux安装JDK完整步骤](https://www.jianshu.com/p/9b987388a518)

#### 配置环境变量

1. JAVA_HOME
2. CLASSPATH
3. PATH

### 安装`hadoop`

#### [下载](http://hadoop.apache.org/)获取二进制压缩包。

#### 解压到指定目录并新建tmp目录,设置环境变量（PATH）即可。

### hadoop集群配置（`非安全`）

1. Hadoop的Java配置由两种类型的重要配置文件驱动

```
只读默认配置 - core-default.xml, hdfs-default.xml, yarn-default.xml and mapred-default.xml.
特定站点的配置 - etc/hadoop/core-site.xml, etc/hadoop/hdfs-site.xml, etc/hadoop/yarn-site.xml and etc/hadoop/mapred-site.xml

```
2. 另外，您可以通过在`etc/hadoop/hadoop-env.sh`和`etc/hadoop/ yarn-env.sh`中设置站点特定的值来控制在发行版的bin目录中找到的Hadoop脚本。
3. HDFS守护进程是NameNode、SecondaryNameNode（`主要做文件的合并工作`）和DataNode。YARN守护进程是ResourceManager、NodeManager和WebAppProxy。如果要使用MapReduce，那么MapReduceJobHistoryServer也将运行。对于大型安装，它们通常运行在不同的主机上。

#### 特定站点的配置

##### 环境变量

1. 使用etc/hadoop/hadoop-env.sh以及可选的etc/hadoop/mapred-env.sh和etc/hadoop/yarn-env.sh脚本对Hadoop守护进程的处理环境进行特定于站点的自定义。
2. 至少配置一下`JAVA_HOME`.（`还可配一下user`）
3. 可以使用下表中显示的配置选项来配置各个守护程序:

| 守护进程| 环境变量 | 描述 |
|--|--|--|
|NameNode	                             |HDFS_NAMENODE_OPTS|例如`export HDFS_NAMENODE_OPTS="-XX:+UseParallelGC -Xmx4g"`|
|NameNode                                | HDFS_NAMENODE_USER |默认为hdfs，可以设置为root|
|DataNode	                                |HDFS_DATANODE_OPTS||
|DataNode                                   | HDFS_DATANODE_USER |默认为hdfs，可以设置为root|
|Secondary NameNode	        |HDFS_SECONDARYNAMENODE_OPTS||
|Secondary NameNode          | HDFS_SECONDARYNAMENODE_USER |默认为hdfs，可以设置为root|
|ResourceManager	               |YARN_RESOURCEMANAGER_OPTS||
|NodeManager	                       |YARN_NODEMANAGER_OPTS||
|WebAppProxy	                       |YARN_PROXYSERVER_OPTS||
|Map Reduce Job History Server	|MAPRED_HISTORYSERVER_OPTS||

4. 其他有用的参数

```
HADOOP_PID_DIR - 目录 the daemons’ process id files are stored.
HADOOP_LOG_DIR - 目录 the daemons’ log files are stored. Log files are automatically created if they don’t exist.
HADOOP_HEAPSIZE_MAX - The maximum amount of memory to use for the Java heapsize. Units supported by the JVM are also supported here. If no unit is present, it will be assumed the number is in megabytes. By default, Hadoop will let the JVM determine how much to use. This value can be overriden on a per-daemon basis using the appropriate _OPTS variable listed above. For example, setting HADOOP_HEAPSIZE_MAX=1g and HADOOP_NAMENODE_OPTS="-Xmx5g" will configure the NameNode with 5GB heap.
```
5. 在大多数情况下，您应该指定HADOOP PID DIR和HADOOP LOG DIR目录，以便它们只能由将要运行HADOOP守护进程的用户写入。否则，可能会出现符号链接攻击。
6. 在/etc/profile.d配置HADOOP_HOME

```
  HADOOP_HOME=/path/to/hadoop
  export HADOOP_HOME
```

##### 配置Hadoop守护进程

###### etc/hadoop/core-site.xml（）

参数	|值| 描述 | 重要程度
--|--|--|--
fs.defaultFS | 访问HDFS文件系统的URI,默认端口是8020 |	hdfs://host:port/ 主节点 | 1
io.file.buffer.size	|131072|	Size of read/write buffer used in SequenceFiles.
hadoop.tmp.dir |  | 默认为`/tmp/hadoop-${user.name}`,最好不要定义在/tmp中，默认namenode和datanode会用到
hadoop.http.staticuser.user |	静态用户，默认dr.who	 | The user name to filter as, on static web filters while rendering content. An example use is the HDFS web UI (user to be used for browsing files).
io.compression.codecs	||	逗号分隔的压缩编解码器类列表，可用于压缩/解压缩

###### etc/hadoop/hdfs-site.xml

1. NameNode配置

参数 |	值|	描述
--|--|--
dfs.namenode.name.dir |	本地文件系统路径用于存放（元数据信息） namespace and transactions logs persistently.	| 如果这是一个以逗号分隔的目录列表，那么为了冗余，名称表将复制到所有目录中。默认为 `file://${hadoop.tmp.dir}/dfs/name`，一般都改一下
dfs.hosts / dfs.hosts.exclude	List of permitted/excluded DataNodes.	If necessary, use these files to control the list of allowable datanodes.
dfs.blocksize	268435456	HDFS blocksize of 256MB for large file-systems.
dfs.namenode.handler.count	100	More NameNode server threads to handle RPCs from large number of DataNodes.
dfs.replication | 默认为3| 数据副本数
dfs.namenode.secondary.http-address	|默认0.0.0.0:9868 | The secondary namenode http server address and port.

2. DataNode配置

参数 |	值|	描述
--|--|--
dfs.datanode.data.dir |	Comma separated list of paths on the local filesystem of a DataNode 用于存放数据的blocks. |如果这是一个以逗号分隔的目录列表，那么为了冗余，数据将复制到所有目录中。默认为 `file://${hadoop.tmp.dir}/dfs/data`，一般都改一下

###### etc/hadoop/yarn-site.xml

1. ResourceManager和NodeManager配置。

Parameter	Value	Notes
yarn.acl.enable	true / false	Enable ACLs? Defaults to false.
yarn.admin.acl	Admin ACL	ACL to set admins on the cluster. ACLs are of for comma-separated-usersspacecomma-separated-groups. Defaults to special value of * which means anyone. Special value of just space means no one has access.
yarn.log-aggregation-enable	false	Configuration to enable or disable log aggregation

2. ResourceManager单独配置

Parameter	Value	Notes
yarn.resourcemanager.address	ResourceManager host:port for clients to submit jobs.	host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.scheduler.address	ResourceManager host:port for ApplicationMasters to talk to Scheduler to obtain resources.	host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.resource-tracker.address	ResourceManager host:port for NodeManagers.	host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.admin.address	ResourceManager host:port for administrative commands.	host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.webapp.address	ResourceManager web-ui host:port.	host:port If set, overrides the hostname set in yarn.resourcemanager.hostname.
yarn.resourcemanager.hostname	ResourceManager host.	host Single hostname that can be set in place of setting all yarn.resourcemanager*address resources. Results in default ports for ResourceManager components.
yarn.resourcemanager.scheduler.class	ResourceManager Scheduler class.	CapacityScheduler (recommended), FairScheduler (also recommended), or FifoScheduler. Use a fully qualified class name, e.g., org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler.
yarn.scheduler.minimum-allocation-mb	Minimum limit of memory to allocate to each container request at the Resource Manager.	In MBs
yarn.scheduler.maximum-allocation-mb	Maximum limit of memory to allocate to each container request at the Resource Manager.	In MBs
yarn.resourcemanager.nodes.include-path / yarn.resourcemanager.nodes.exclude-path	List of permitted/excluded NodeManagers.	If necessary, use these files to control the list of allowable NodeManagers.

3. NodeManager单独配置

参数	|值| 描述 | 重要程度
--|--|--|--
yarn.nodemanager.resource.memory-mb	Resource i.e. available physical memory, in MB, for given NodeManager	Defines total available resources on the NodeManager to be made available to running containers
yarn.nodemanager.vmem-pmem-ratio	Maximum ratio by which virtual memory usage of tasks may exceed physical memory	The virtual memory usage of each task may exceed its physical memory limit by this ratio. The total amount of virtual memory used by tasks on the NodeManager may exceed its physical memory usage by this ratio.
yarn.nodemanager.local-dirs	Comma-separated list of paths on the local filesystem where intermediate data is written.	Multiple paths help spread disk i/o.
yarn.nodemanager.log-dirs	Comma-separated list of paths on the local filesystem where logs are written.	Multiple paths help spread disk i/o.
yarn.nodemanager.log.retain-seconds	10800	Default time (in seconds) to retain log files on the NodeManager Only applicable if log-aggregation is disabled.
yarn.nodemanager.remote-app-log-dir	/logs	HDFS directory where the application logs are moved on application completion. Need to set appropriate permissions. Only applicable if log-aggregation is enabled.
yarn.nodemanager.remote-app-log-dir-suffix	logs	Suffix appended to the remote log dir. Logs will be aggregated to ${yarn.nodemanager.remote-app-log-dir}/${user}/${thisParam} Only applicable if log-aggregation is enabled.
yarn.nodemanager.aux-services |	mapreduce_shuffle | 可在 NodeManager 上运行的扩展服务，需配置成 mapreduce_shuffle，才可运行 MapReduce 程序。	| 1
yarn.nodemanager.env-whitelist	Environment properties to be inherited by containers from NodeManagers	For mapreduce application in addition to the default values HADOOP_MAPRED_HOME should to be added. Property value should JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPAT

4. Configurations for History Server (Needs to be moved elsewhere)

Parameter	Value	Notes
yarn.log-aggregation.retain-seconds	-1	How long to keep aggregation logs before deleting them. -1 disables. Be careful, set this too small and you will spam the name node.
yarn.log-aggregation.retain-check-interval-seconds	-1	Time between checks for aggregated log retention. If set to 0 or a negative value then the value is computed as one-tenth of the aggregated log retention time. Be careful, set this too small and you will spam the name node.

###### etc/hadoop/mapred-site.xml

1. MapReduce Applications配置

参数	|值| 描述 | 重要程度
--|--|--|--
mapreduce.framework.name |	yarn |	指定mapreduce的执行环境（默认为本地） | 1
mapreduce.map.memory.mb	1536	Larger resource limit for maps.
mapreduce.map.java.opts	-Xmx1024M	Larger heap-size for child jvms of maps.
mapreduce.reduce.memory.mb	3072	Larger resource limit for reduces.
mapreduce.reduce.java.opts	-Xmx2560M	Larger heap-size for child jvms of reduces.
mapreduce.task.io.sort.mb	512	Higher memory-limit while sorting data for efficiency.
mapreduce.task.io.sort.factor	100	More streams merged at once while sorting files.
mapreduce.reduce.shuffle.parallelcopies	50	Higher number of parallel copies run by reduces to fetch outputs from very large number of maps.

2. MapReduce JobHistory Serve

Parameter	Value	Notes
mapreduce.jobhistory.address	MapReduce JobHistory Server host:port	Default port is 10020.
mapreduce.jobhistory.webapp.address	MapReduce JobHistory Server Web UI host:port	Default port is 19888.
mapreduce.jobhistory.intermediate-done-dir	/mr-history/tmp	Directory where history files are written by MapReduce jobs.
mapreduce.jobhistory.done-dir	/mr-history/done	Directory where history files are managed by the MR JobHistory Server.

###### 健康检查配置

1. Hadoop提供了一种机制，管理员可以通过这种机制配置NodeManager，以便定期运行管理员提供的脚本，以确定节点是否健康。

`etc/hadoop/yarn-site.xml`

Parameter	Value	Notes
yarn.nodemanager.health-checker.script.path	Node health script	Script to check for node’s health status.
yarn.nodemanager.health-checker.script.opts	Node health script options	Options for script to check for node’s health status.
yarn.nodemanager.health-checker.interval-ms	Node health script interval	Time interval for running health script.
yarn.nodemanager.health-checker.script.timeout-ms	Node health script timeout interval	Timeout for health script execution.


###### 修改etc/hadoop/workers
`将从机主机名（IP的映射）写进去`
2. 它不用于任何基于java的Hadoop配置。为了使用此功能，必须为用于运行Hadoop的帐户建立ssh信任(通过无密码的ssh或其他方法，如Kerberos)。

###### 配置机架感知
1. 强烈建议在启动HDFS之前配置机架感知。

###### hadoop日志配置

1. Hadoop通过Apache Commons Logging框架使用Apache log4j进行日志记录。 编辑etc / hadoop / log4j.properties文件以自定义Hadoop守护程序的日志配置（日志格式等）。


#### 配置本地网络/etc/hosts，主机名和IP的映射

#### 使主机名生效`/etc/sysconfig/network`中的hostname

#### 关闭防火墙（iptables）。

`/etc/init.d/iptables stop`

#### 关闭SELinux。

`setenforce 0`
`getenforce`

#### 主从相互之间ssh免密登录

`相同的私钥和公钥`


##  HA Cluster，即高可用集群模式

1. 一般来说，分为NN的高可用和RM的高可用。在完全分布式的基础上，增加备用NN和RM节点。
2. NN高可用，也就是集群里面会部署两台NN（最多也只能两台），以形成主备NN节点，达到高可用的目的。RM高可用与NN高可用类似，也是在集群里部署备用RM节点。
3. 主备模式。当Active的NN/RM出现问题无法工作时，Standby的那台则立即无缝切入，继续保障集群正常运转。
4. 这种模式是很多企业都使用的，但是依然有缺陷。性能瓶颈依然存在——仅有一台NN/RM，由于无法横向扩展，其很可能会超负载运行。


## HA + Federation Cluster，即高可用联邦集群模式

1. 解决了单纯HA模式的性能瓶颈。
2. 整个HA集群再划分为两个以上的集群，不同的集群之间通过Federation进行连接。
3. 不同集群间可以共享数据节点，也可以不共享，可以互相访问和操作数据，也可以不。`集群之间的数据相互独立，不能不经过NN访问DN的数据`
4. 这样便做到了HA集群的横向扩展，从而移除了单纯HA模式同时仅有1台NN/RM工作所带来的性能瓶颈。
5. Federation模式，相当于在多个集群之上又构建了一个集群层次。
6. 从数据访问的角度看，也可以简单的将其理解为一台路由器，而每一个HA集群则是单独的网络，不同网络间通过`Federation路由器进行沟通`。
7. 此模式是目前hadoop生态中最高的一种模式，适用于规模较大的企业。

作者：mtide_net
链接：https://www.jianshu.com/p/c3a834e45ae3
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 启动

1. 完成所有必要的配置后，将文件分发到所有机器上的HADOOP CONF DIR目录。这应该是所有机器上的相同目录。
2. 通常，建议HDFS和YARN作为单独的用户运行。在大多数安装中，HDFS进程作为HDFS执行。YARN通常使用YARN帐户。

### 第一次启动

1. 必须格式化namenode。生成集群元数据，生成fsimage(持久化)。
```
$HADOOP_HOME/bin/hdfs namenode -format <cluster_name>
```
2. 启动集群。`./start-all.sh`

## 查看集群状态

1. `jps`查看进程。
2. `./hadoop fs -ls /`

## 查看端口

|进程|端口|功能|
|--|--|--|
|namenode|9870||
|namenode|9000||
|datanode|9864||
|datanode|9866||
|datanode|9867||
|datanode|40599||
|resourcemanager|8030||
|resourcemanager|8031||
|resourcemanager|8032||
|resourcemanager|8033||
|resourcemanager|8088||
|nodemanager|8040||
|nodemanager|8042||
|nodemanager|13562||
|nodemanager|39547||
|historyserver|8188||
|historyserver|10200||

## hadoop命令

### hdfs

1. HDFS 上面的文件，`只能创建和删除`，无法更新一个存在的文件，如果要更新 HDFS 上的文件，需要先删除这个文件，然后提交最新的文件即可。

```
hdfs namenode -format    // 格式化namenode
hdfs --daemon start namenode    // 启动namenode
hadoop fs -ls /    //查看 hdfs 根目录数据
hadoop fs -mkdir /logs    //在 hdfs 根目录创建一个 logs 目录
hadoop fs -put /data/test.txt /logs    // 从本地上传一个文件到 hdfs 的 /logs 目录下
hadoop fs -cat /logs/test.txt    // 查看 hdfs 中一个文本文件的内容
hadoop fs -text /logs/db.gz    // HDFS 上的压缩文件通过“-text”参数也能直接查看，因为默认情况下 Hadoop 会自动识别常见的压缩格式
hadoop fs  -rm  -r /logs/test.txt    // 删除 hdfs 上一个文件
```

### Mapreduce

1. `share/hadoop/mapreduce`，找到一个名为 hadoop-mapreduce-examples-3.2.1.jar 的 jar 文件.包含了一个 wordcount 功能，它主要功能是用来统计一系列文本文件中每个单词出现的次数。

```
hadoop jar /opt/hadoop/current/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar  wordcount /demo  /output
```

2. 最后的两个路径都是 HDFS 上的路径，第一个路径是分析读取文件的目录，`必须存在`；第二个路径是分析任务输出结果的存放路径，`必须不存在`，分析任务会自动创建这个目录。
3. part-r-00000，表示输出文件名，常见的名称有 part-m-00000、part-r-00001，其中，带 m 标识的文件是 mapper 输出，带 r 标识的文件是 reduce 输出的，00000 为 job 任务编号，part-r-00000 整个文件为结果输出文件。
4. 默认 mapreduce 的运行环境是 local（本地），要让 mapreduce 在 yarn 上运行，需要做几个参数配置就行了。
