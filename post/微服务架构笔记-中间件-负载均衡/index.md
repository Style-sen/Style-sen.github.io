# 负载均衡\反向代理


## 负载均衡的作用

### 转发功能

按照一定的算法【权重、轮询】，将客户端请求转发到不同应用服务器上，减轻单个服务器压力，提高系统并发量。

### 故障移除

通过心跳检测的方式，判断应用服务器当前是否可以正常工作，如果服务器期宕掉，自动将请求发送到其他应用服务器。

### 恢复添加

如检测到发生故障的应用服务器恢复工作，自动将其添加到处理用户请求队伍中。

## OSPF负载均衡

1. OSPF（开放式最短链路优先）是一个内部网关协议（Interior Gateway Protocol，简称 IGP）。
2. OSPF 通过路由器之间通告网络接口的状态来建立链路状态数据库，生成最短路径树，OSPF 会自动计算路由接口上的 Cost 值，但也可以通过手工指定该接口的 Cost 值，手工指定的优先于自动计算的值。
3. OSPF 计算的 Cost，同样是和接口带宽成反比，带宽越高，Cost 值越小。到达目标相同 Cost 值的路径，可以执行负载均衡，最多 6 条链路同时执行负载均衡。

## LVS

1. 它是一种集群（Cluster）技术，采用 IP 负载均衡技术和基于内容请求分发技术。
2. 调度器具有很好的吞吐率，将请求均衡地转移到不同的服务器上执行，且调度器自动屏蔽掉服务器的故障，从而将一组服务器构成一个高性能的、高可用的虚拟服务器。

## [HAProxy](https://www.haproxy.org/)可靠，高性能的TCP / HTTP负载均衡器

haproxy是工作在四层之上的，这也就意味着它可以代理的服务要比nginx更广泛，性能比nginx也要更高一些。

1. [让一个端口同时做两件事：http/https和ssh](https://blog.csdn.net/muye0503/article/details/45721411)
2. 需要设置为开机启动。

## NGINX

### Nginx的负载分发策略

1. Nginx 的 upstream目前支持的分配算法： 

    * 轮询 ——1：1 轮流处理请求（默认）,每个请求按时间顺序逐一分配到不同的应用服务器，如果应用服务器down掉，自动剔除，剩下的继续轮询。 
    * 权重 ——you can you up.通过配置权重，指定轮询几率，权重和访问比率成正比，用于应用服务器性能不均的情况。 
    * ip_哈希算法:每个请求按访问ip的hash结果分配，这样`每个访客固定访问一个应用服务器`，可以解决session共享的问题。 

### 配置Nginx的负载均衡与分发策略

1. 通过在upstream参数中添加的应用服务器IP后添加指定参数即可实现，如：

```
upstream tomcatserver1 {
    server 192.168.72.49:8080 weight=3;
    server 192.168.72.49:8081;
} 
 
server {
    listen       80;
    server_name  8080.max.com;
    #charset koi8-r;
    #access_log  logs/host.access.log  main;
    location / {
        proxy_pass   http://tomcatserver1;
        index  index.html index.htm;
    }
} 
```

2. 通过以上配置，便可以实现，在访问8080.max.com这个网站时，由于配置了proxy_pass地址，所有请求都会先通过nginx反向代理服务器，在服务器将请求转发给目的主机时，读取upstream为 tomcatsever1的地址，读取分发策略，配置tomcat1权重为3，所以nginx会将大部分请求发送给49服务器上的tomcat1，也就是8080端口；较少部分给tomcat2来实现有条件的负载均衡，当然这个条件就是服务器1、2的硬件指数处理请求能力。 

3. nginx其他配置

```
upstream myServer {  
    server 192.168.72.49:9090 down; 
    server 192.168.72.49:8080 weight=2; 
    server 192.168.72.49:6060; 
    server 192.168.72.49:7070 backup; 
}
```

* down:表示单前的server暂时不参与负载
* Weight:默认为1.weight越大，负载的权重就越大。
* max_fails:允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误
* fail_timeout:max_fails 次失败后，暂停的时间。
* Backup:其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

### 使用Nginx的高可用 

1. 除了要实现网站的高可用，也就是提供n多台服务器用于发布相同的服务，添加负载均衡服务器分发请求以保证在高并发下各台服务器能相对饱和的处理请求。同样，负载均衡服务器也需要高可用，以防如果负载均衡服务器挂掉了，后面的应用服务器也紊乱无法工作。
2. 实现高可用的方案：添加冗余。添加n台nginx服务器以避免发生上述单点故障。具体方案详见下文：keepalive+nginx实现负载均衡高可用.

### keepalive+nginx实现负载均衡高可用

1. 为了避免负载均衡服务器的宕机故障，需要建立一个备份机。主备机上都运行高可用（High Availability）监控程序，通过传送心跳信息来监控对方的运行状况。当备份机不能在一定的时间内收到对方的正常心跳时，它就接管主服务器的服务IP并继续提供负载均衡服务；当备份管理器又从主管理器收到“I am alive”这样的信息时，它就释放服务IP地址，这样的主服务器就开始再次提供负载均衡服务。

#### keepalive 安装

#### keepalive 配置

## PACHE

### 反向代理

```conf
<VirtualHost *:80>
    ServerName www.songsong.red
    ServerAlias songsong.red

    ProxyRequests off  # off表示开启反向代理，on表示开启正向代理

    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    <Location />
        ProxyPass http://localhost:8080/
        ProxyPassReverse http://localhost:8080/
    </Location>
</VirtualHost>
```

## [TRAEFIK](https://github.com/containous/traefik)

1. 云原生Edge路由器。
2. 是一个现代HTTP反向代理和负载均衡器，使部署微服务更容易. 
