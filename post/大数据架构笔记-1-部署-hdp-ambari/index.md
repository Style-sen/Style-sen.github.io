# 大数据架构笔记-1-集群安装-HDP-AMBARI


参考[HDP Installation Documentation](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/installation.html)
参考[amarmesic/ambari-hdp-docker](https://github.com/amarmesic/ambari-hdp-docker)

## 概述

1. 版本依赖.https://supportmatrix.hortonworks.com。


## Apache Ambari Installation

### 准备

#### Set Up Password-less SSH

#### Set Up Service User Accounts

#### Enable NTP on the Cluster and on the Browser Host

1. 启用NTP服务。

#### Check DNS and NSCD

1. 使用docker-compose部署的时候，使用hostname参数。

#### Configuring iptables

1. 关闭防火墙。

#### Disable SELinux and PackageKit and check the umask Value

1. 关闭SELinux。

#### Download and set up database connectors

1. 像Druid, Hive, Ranger, Oozie和Superset这样的组件需要一个可操作的数据库。
2. 在安装过程中，您可以选择使用现有的数据库，或者让Ambari安装一个新的实例(对于Hive)。
3. 为了让Ambari连接到您选择的数据库，您必须在安装组件之前从数据库供应商直接下载必要的数据库驱动程序和连接器。
4. 为了更好地为安装或升级做准备，请在设置环境时设置数据库连接器。

##### Configuring a Database Instance for Ranger

1. MySQL、Oracle、PostgreSQL或Amazon RDS数据库实例必须运行并可供Ranger使用。Ranger安装将创建两个新用户(默认名称:rangeradmin和rangerlogger)和两个新数据库(默认名称:Ranger和Ranger audit)。
2. `可以创建好mysql数据库，在Ambari安装向导过程中，指定连接数据`。

##### Install Databases for HDF services

1. 在安装Schema Registry,、SAM、Druid和Superset时，需要一个关系数据存储来存储元数据。
2. 您可以使用MySQL、Postgres、Oracle或MariaDB。
3. 如果使用Superset在现有的HDP集群上安装，可以跳过安装说明，因为MySQL是与Druid一起安装的。在这种情况下，配置数据库。

### 安装Ambri

1. 从2.7.5开始，使用源安装，需要使用用户和密码下载源文件。

### 安装、配置和部署一个集群 

1. `配置过程中需要指定现有的数据库`。

#### 启动服务

1. `ambari-server start`
2. `ambari-server status`
3. `ambari-server stop`
4. `如果您计划为Hive或Oozie使用一个现有的数据库实例，那么您必须在安装Hadoop集群之前准备好使用一个现有的数据库。`
5. 在启动时，会对数据库一致性进行检查。如果出现任何问题，启动会中止，并报告`DB configs consistency check failed`。

#### 登入

#### 启动安装向导

#### 选择版本

#### Install Options

#### Confirm Hosts

#### Choose Services

#### Assign Masters

#### Assign Slaves and Clients

#### Customize Services

#### Review

#### Install, Start and Test

#### Complete

## 遇到问题


