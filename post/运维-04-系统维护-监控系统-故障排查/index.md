# 运维-监控系统-问题排查


# 监控系统

## [Grafana]() *****
1. Grafana是一个跨平台的开源的度量分析和可视化工具，可以通过将采集的数据查询然后可视化的展示，并及时通知。

### 核心特点
展示方式：快速灵活的客户端图表，面板插件有许多不同方式的可视化指标和日志，官方库中具有丰富的仪表盘插件，比如热图、折线图、图表等多种展示方式；
数据源：Graphite，InfluxDB，OpenTSDB，Prometheus，Elasticsearch，CloudWatch和KairosDB等；
通知提醒：以可视方式定义最重要指标的警报规则，Grafana将不断计算并发送通知，在数据达到阈值时通过Slack、PagerDuty等获得通知；
混合展示：在同一图表中混合使用不同的数据源，可以基于每个查询指定数据源，甚至自定义数据源；
注释：使用来自不同数据源的丰富事件注释图表，将鼠标悬停在事件上会显示完整的事件元数据和标记；
过滤器：Ad-hoc过滤器允许动态创建新的键/值过滤器，这些过滤器会自动应用于使用该数据源的所有查询。

### 部署
1. [官方镜像grafana/grafana](https://hub.docker.com/r/grafana/grafana)
2. 环境变量
```yml
GF_INSTALL_PLUGINS  # 指定插件版本
GF_SECURITY_ADMIN_PASSWORD # admin的密码
```
3. 挂载Volumns
```yml
volumes:
  - $PWD/extra/grafana_db:/var/lib/grafana grafana/grafana
```
4. 部署完之后，需要登录进去创建`数据源`和创建`DashBoard`。

#### 持久化
1. Grafana存储的配置以及图表在自己生成的sqlit数据库中长期存储，并不友好。
2. 可以使用mysql

## [prometheus](https://prometheus.io/docs/introduction/overview/) *****
1. 2012年，2016年加入CNCF；
2. 参考[Prometheus实战](https://legacy.gitbook.com/book/songjiayang/prometheus)

### 核心特点
1. 提供包含时间序列的强大的多维数据模型，identified by metric name and key/value pairs，如Counter、Gauge、Histogram、Summary；
2. `PromQL`，自研一套`高性能的时序数据库`，在V3版本可以达到每秒千万级别的数据存储；
3. 不依赖分布式存储；
4. 提供pull模式、push gateway方式实现时间序列数据的采集；
5. 通过服务发现或静态配置发现目标；
6. 多种图形和仪表板支持模式,还支持导出（Grafana、Web UI、API clients）；
7. 通过中间网关支持推送时间序列。
8. 【优点】不仅支持SWAM原生集群；还支持K8S集群的监控；是目前容器监控最好的解决方案。
9. 【优点】Go凭借简洁的语法和优雅的并发，在Java占据业务开发，C占领底层开发的情况下，准确定位中间件开发需求，在当前开源中间件产品中被广泛应用。
10. 【优点】Prometheus开始成为主导及容器监控方面的标配，并且在未来可见的时间内被广泛应用。

### 组件（可选）
1. Main Server，负责`抓取pull（周期性）和存储`时间序列数据。
2. 客户端代码库，集成在业务代码中。
3. Push gateway;
4. special-purpose exporters;
5. an alertmanager to handle alerts;
6. various support tools.

### 数据模型

### 架构及生态组件

### 应用场景

### 部署
1. [官方镜像prom/prometheus](https://hub.docker.com/r/prom/prometheus)

#### 配置文件(/etc/prometheus/prometheus.yml)docker 运行时挂载
```yml
global:
    scrape_interval: 15s
    # evaluation_interval: 15s    # Evaluate rules every 15 seconds. The default is every 1 minute.
    # scrape_timeout is set to the global default (10s).
    external_labels:    # 与外部通信时的附加标签
      monitor: 'raysonxin-monitor'

scrape_configs:  # 抓取配置 
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']    # 目标主机(本地server)
        labels:
          group: 'local'

  - job_name: 'bn'
    scrape_interval: 5s
    static_configs:
      - targets: ['api_host:9000'] # 目标主机（业务主机）
        labels:
          group: 'arithmetic'

```

#### docker-compose
```yml
    volumes:
      - /prometheus/prometheus/config:/etc/prometheus
      - /prometheus/prometheus/data:/prometheus
    command:
      - ‘--config.file=/etc/prometheus/prometheus.yml‘
      - ‘--log.level=info‘
      - ‘--web.listen-address=0.0.0.0:9090‘
      - ‘--storage.tsdb.path=/prometheus‘
      - ‘--storage.tsdb.retention=15d‘
      - ‘--query.max-concurrency=50‘
      - ‘--web.enable-lifecycle‘
```

#### 数据持久化
1. Prometheus的存储数据库默认只保留15天的数据.
2. 可以使用InfluxDB

## [Rollbar](https://rollbar.com/)    
    实时收集分析Web应用抛出的错误.免费版一个月5000次错误事件。

## SENTRY

### 使用docker安装
1. [Installation with Docker](https://docs.sentry.io/server/installation/docker/)

## ZABBIX
1. 一个基于WEB界面的提供分布式系统监视以及网络监视功能的企业级的开源解决方案。
2. 2012年；

### 核心特点
2. 可以运行在Linux，Solaris，HP-UX，AIX，Free BSD，Open BSD，OS X等平台上；
3. 【缺点】Zabbix由于使用了关系型数据存储时序数据，所以在监控大规模集群时常常在数据存储方面捉襟见肘。所以从Zabbix 4.2版本后开始支持TimescaleDB时序数据库，不过目前成熟度还不高。
4. 【缺点】对容器支持不是很好。

### 组件
1. zabbix由2部分构成，zabbix server与可选组件zabbix agent。
1. zabbix server可以通过SNMP，zabbix agent，ping，端口监视等方法提供对远程服务器/网络状态的监视，数据收集等功能；
3. Zabbix Server将收集的监控数据存储到Zabbix Database中。Zabbix Database支持常用的关系型数据库，如果MySQL、PostgreSQL、Oracle等，默认是MySQL，并提供Zabbix Web页面（PHP编写）数据查询。
3. Agent主要负责采集数据并通过`主动或者被动`的方式采集数据发送到Server/Proxy，除此之外，为了扩展监控项，Agent还支持执行自定义脚本。


### 应用场景
```
作者：徐亮偉
链接：https://www.zhihu.com/question/19973178/answer/154424211
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 硬件监控
1. 通过SNMP来进行路由器交换机的监控(这些可以跟一些厂商沟通来了解如何做)、服务器的温度以及其他，可以通过IPMI来实现。当然如果没有`硬件全都是云，直接跳过这一步骤`。

#### 系统监控。 
1. 如CPU的负载，上下文切换、内存使用率、磁盘读写、磁盘使用率、磁盘inode节点。当然这些都是`需要配置触发器`，因为默认太低会频繁报警。

#### 服务监控。
1. 比如用LNMP nginx自带Status模块、PHP也有相关的Status、MySQL的话可以通过percona来进行监控。Redis这些通过自身的info获信息进行过滤等。方法都类似。要么服务自带。要么通过脚本来实现想监控的内容，以及报警和图形功能。

#### 网络监控。
1. 如果`是云主机又不是跨机房，那么可以选择不监控网络`。当然你说我们是跨机房以及如何如何。推荐使用smokeping来做网络相关的监控。或者直接交给你们的网络工程师来做。因为术业有专攻。

#### 安全监控。
1. 如果是`云主机可以考虑使用自带的安全防护`。当然也可以使用iptables。如果是硬件，那么推荐使用硬件防火墙。使用云可以购买防DDOS，避免出现故障导致down机一天。如果是系统，那么权限、密码、备份、恢复基础的方案要做好。web同时也可以使用nginx+waf来实现一个web层面的防火墙。当然也可以使用集成好的openresty。

#### Web监控。
1. web监控的话题其实还是很多。比如可以使用自带的web监控来监控`页面`相关的延迟、js响应时间、下载时间、等等。这里我推荐使用专业的商业软件，听云来做这一块。毕竟人家全国各地都有机房。（如果本身是多机房那就另说了）

#### 日志监控。
1. 如果是web的话可以使用监控Nginx的500x日志。PHP的ERROR日志。其实这些需求无非是，收集、存储、查询、展示，我们其实可以使用开源的ELKstack来实现。ogstash（收集）、elasticsearch（存储+搜索）、kibana（展示）

#### 业务监控(API) ******
1. 所以业务层面这块的监控需要和开发以及总监开会讨论，监控比较重要，如API、等。通过简单的脚本就可以实现，最后设置触发器即可

#### 流量分析。
1. 平时我们分析日志都是拿awk sed  xxx一堆工具来实现。这样对我们统计ip、pv、uv不是很方便。那么可以使用百度统计、google统计、商业，让开发嵌入代码即可。当然避免隐私也可以使用piwiki来做相关的流量分析。

#### 可视化。
通过screen以及引入一些第三方的库来美化界面，同时我们也需要知道、订单量突然增加、突然减少。或者说突然来了一大波流量，这流量从哪儿来，是不是推广了，还是被攻击了。可以结合监控平来来梳理各个系统之间的业务关系。

#### 自动化监控。
1. 可以通过Zabbix的主动模式以及被动模式来实现。当然最好还是通过API来实现。

# 问题排查

## 服务器变慢
1. 服务器整体情况、CPU使用情况、内存、磁盘、磁盘IO、网络IO。

### vmstat(CPU使用情况)
1. 其中主要关注 procs 和 cpu 这两个参数
```
procs ：
r ：运行和等待 CPU 时间片的进程数，一般来说整个系统的运行队列不要超过总核数的 2 倍，要不然系统压力太大了
b : 等待资源的进程数，比如正在等待磁盘 IO ，网络 IO 这种
cpu ：
us  ：用户进程消耗 CPU 时间百分比， us 值高的话，说明用户进程消耗 CPU 时间比较长，如果长期大于 50% 的话，那就说明程序还有需要优化的地方
sy ：内核进程消耗的 CPU 时间百分比
us + sy 参考值为 80% ，如果大于 80% 的话，说明可能存在 CPU 不足
```

### free（内存使用情况）
1. `free`、`free -g`、`free -m`；
2. 如果应用程序可用内存/系统物理内存大于 70% 的话，说明内存是充足的，没啥问题，但是如果小于 20% 的话，就要考虑增加内存了。

### df（磁盘使用情况）

### iostat（磁盘IO）
1. `iostat -xdk 3 2 `
```
rkB/s ：每秒读取数据量 kB ；
wkB/s ：每秒写入数据量 kB ；
svctm ：I/O 请求的平均服务时间，单位毫秒；
util ：一秒中有百分之几的时间用于 I/O 操作，如果接近 100% 说明磁盘带宽跑满了，这个时候就要优化程序或者增加磁盘了
```

### sar（网络IO）
1. `sar -n DEV 3 2`
```
IFACE ：LAN 接口
rxpck/s ：每秒钟接收的数据包
txpck/s ：每秒钟发送的数据包
rxKB/s ：每秒接收的数据量，单位 KByte
txKB/s ：每秒发出的数据量，单位 KByte
rxcmp/s ：每秒钟接收的压缩数据包
txcmp/s ：每秒钟发送的压缩数据包
rxmcst/s：每秒钟接收的多播数据包
```
