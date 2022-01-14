# redis


参考[Redis的最常被问到知识点总结](https://www.cnblogs.com/Young111/p/11518346.html)
参考[Redis性能问题排查解决手册(七)](https://www.cnblogs.com/mushroom/p/4738170.html)
参考[Redis 命令参考](http://redisdoc.com/list/ltrim.html)

1. 解决的问题：`数据库受磁盘IO的影响`。把比较热的数据放在内存中。

## 1. 概述

1. Redis 是一个基于内存的高性能key-value数据库。SQL约束比较强。
2. 整个数据库统统加载在内存当中进行操作，定期通过异步操作把数据库数据flush到硬盘上进行保存。因为是纯内存操作，Redis的性能非常出色，每秒可以处理超过 10万次读写操作，是已知性能最快的Key-Value DB。
3. 主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在`较小数据量的高性能操作和运算`上。
4. Redis业务处理为单线程模式，采用队列模式将并发访问变为串行访问。但是，其他功能也会fork或clone出一个线程执行。
5. CS模式。
6. a.IO能不能读取:使用`多路复用器（epoll）`解决更有效率地获取哪个client可以读取。b.有了，worker自己得去读取。c.计算。
7. `单worker线程，串行执行读客户端-计算-写客户端。原子执行`【这样多个服务访问一个redis，不用加锁】。`串行之后，就会变的快`。
8. redis 6.0增加了IO thread.IO变成多线程，计算是单线程。
9. memcache的key和value都为字符串。redis的value有类型，不同类型调用不同的方法。
10. redis hbase kafka都是二进制安全（只存字符数组，没有类型）。

### 1.1. 涉及到的网络IO

1. `/proc/[进程ID]/fd`目录存的是进程涉及到的文件描述符。1-输入 2-输出 3-错误输出 6-监听 监听到一个客户端会创建一个socket通道与其通信。
2. `BIO`：多线程去读取，没有数据会阻塞。
3. `NIO`：非阻塞。就可以单线程轮询。用户态、内核态切换无数次。
4. `select`: 内核系统调用。可监控多个文件描述符，直到有1个或多个有内容返回结果集（可以读的文件描述符）。多路复用只告诉你谁可以读，需要程序自己去读文件描述符。缺点：内核自主去轮询，参数传的大。
5. [select、poll、epoll之间的区别总结[整理]](https://www.cnblogs.com/Anker/p/3265058.html).

### 1.2. 数据类型

1. Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。
2. 不同的数据类型使用命令不同。
3. 单个value的最大限制是1GB。

#### 1.2.1. string

1. 真的是字符串。
2. 数值计算。
3. bitmap。

##### 1.2.1.1. 常用命令

1. SET 将字符串值 value 关联到 key 。
2. SETNX 只在键 key 不存在的情况下， 将键 key 的值设置为 value 。
3. SETEX 将键 key 的值设置为 value ， 并将键 key 的生存时间设置为 seconds 秒钟。
4. PSETEX 这个命令和 SETEX 命令相似， 但它以毫秒为单位设置 key 的生存时间， 而不是像 SETEX 命令那样以秒为单位进行设置。
5. GET 返回与键 key 相关联的字符串值。
6. GETSET 将键 key 的值设为 value ， 并返回键 key 在被设置之前的旧值。
7. STRLEN 返回键 key 储存的字符串值的长度。
8. APPEND 如果键 key 已经存在并且它的值是一个字符串， APPEND 命令将把 value 追加到键 key 现有值的末尾。
9. SETRANGE 从偏移量 offset 开始， 用 value 参数覆写(overwrite)键 key 储存的字符串值。
10. GETRANGE 返回键 key 储存的字符串值的指定部分， 字符串的截取范围由 start 和 end 两个偏移量决定 (包括 start 和 end 在内)。
11. INCR 为键 key 储存的数字值加上1。
12. INCRBY 为键 key 储存的数字值加上增量 increment 。
13. INCRBYFLOAT 为键 key 储存的值加上浮点数增量 increment 。
14. DECR 为键 key 储存的数字值减去1。
15. DECRBY 将键 key 储存的整数值减去减量 decrement 。
16. MSET 同时为多个键设置值。
17. MSETNX 当且仅当所有给定键都不存在时， 为所有给定键设置值。
18. MGET 返回给定的一个或多个字符串键的值。

位图(bitmap):
1. SETBIT  key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。offset 参数必须大于或等于 0 ，小于 2^32 (bit 映射被限制在 512 MB 之内)。
2. GETBIT 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。
3. BITCOUNT 计算给定字符串中，被设置为 1 的比特位的数量,从开始（字符索引）到结束。
4. BITPOS 返回位图中第一个值为 bit 的二进制位的位置。
5. BITOP 对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。
6. BITFIELD 

##### 1.2.1.2. 应用场景

1. String是最常用的一种数据类型，普通的key/value存储都可以归为此类；
2. 可以做`点赞`（数值类型）。但是hash更适合点赞。
3. 可以做`统计`（数值）。
4. 可以做`限流` (数值)。
5. 防止`超卖`.
7. `统计任意用户任意时间窗的登录天数`（bitmap）365bit。
8. 基于7实现`活跃用户`.时间作为key，用户Id为位，位或（bitmap）。

##### 1.2.1.3. 实现方式

#### 1.2.2. hash

##### 1.2.2.1. 常用命令

1. HSET 将哈希表 key 中域 field 的值设置为 value,`会覆盖` 。
2. HSETNX 当且仅当域 field 尚未存在于哈希表的情况下， 将它的值设置为 value 。
3. HGET `返回哈希表中给定域的值`。
4. HEXISTS 检查给定域 field 是否存在于哈希表 hash 当中。
5. HDEL `删除哈希表 key 中的一个或多个指定域，不存在的域将被忽略`。
6. HLEN `返回哈希表 key 中域的数量`。
7. HSTRLEN 返回哈希表 key 中， 与给定域 field 相关联的值的字符串长度（string length）。
8. HINCRBY 为哈希表 key 中的域 field 的值加上增量 increment 。
9. HINCRBYFLOAT 为哈希表 key 中的域 field 加上浮点数增量 increment 。
10. HMSET `同时将多个 field-value (域-值)对设置到哈希表 key 中`。
11. HMGET `返回哈希表 key 中，一个或多个给定域的值`。
12. HKEYS 返回哈希表 key 中的所有域。
13. HVALS `返回哈希表 key 中所有域的值`。
14. HGETALL `返回哈希表 key 中，所有的域和值`。
15. HSCAN

##### 1.2.2.2. 应用场景

1. HASH MAP。
2. 商品详情页。
3. 用户统计值（好友数、点赞数）。

##### 1.2.2.3. 实现方式

#### 1.2.3. list

1. 双向链表。Redis 列表是简单的`字符串`列表，按照插入顺序排序。

##### 1.2.3.1. 常用命令

1. LPUSH 将一个或多个值 value 插入到列表 key 的`表头`。
2. LPUSHX 将值 value 插入到列表 key 的表头，当且仅当 key 存在并且是一个列表。
3. RPUSH 将一个或多个值 value 插入到列表 key 的`表尾(最右边)`。
4. RPUSHX 将值 value 插入到列表 key 的表尾，当且仅当 key 存在并且是一个列表。
5. LPOP 移除并返回列表 key 的头元素。
6. RPOP 移除并返回列表 key 的尾元素。
7. RPOPLPUSH 命令 RPOPLPUSH 在一个原子时间内，执行以下两个动作：将列表 source 中的最后一个元素(尾元素)弹出，并返回给客户端。将 source 弹出的元素插入到列表 destination ，作为 destination 列表的的头元素。
8. LREM 根据参数 count 的值，移除列表中与参数 value 相等的元素。
9. LLEN 返回列表 key 的长度。
10. LINDEX `返回列表 key 中，下标为 index 的元素`。
11. LINSERT 将值 value 插入到列表 key 当中，位于值 pivot 之前或之后。
12. LSET 将列表 key 下标为 index 的元素的值设置为 value 。
13. LRANGE `返回列表 key 中指定区间内的元素，区间以偏移量 start 和 stop 指定。`
14. LTRIM `对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。`
15. BLPOP BLPOP 是列表的阻塞式(blocking)弹出原语。
16. BRPOP BRPOP 是列表的阻塞式(blocking)弹出原语。
17. BRPOPLPUSH BRPOPLPUSH 是 RPOPLPUSH source destination 的阻塞版本，当给定列表 source 不为空时， BRPOPLPUSH 的表现和 RPOPLPUSH source destination 一样。

##### 1.2.3.2. 应用场景

1. 栈
2. 队列
3. 评论列表。
4. 服务无状态。

##### 1.2.3.3. 实现方式

#### 1.2.4. set

1. 无序。
2. 去重.

##### 1.2.4.1. 常用命令

1. SADD 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略。
2. SISMEMBER 判断 member 元素是否集合 key 的成员。
3. SPOP 移除并返回集合中的一个随机元素。
4. SRANDMEMBER
5. SREM 移除集合 key 中的一个或多个 member 元素，不存在的 member 元素会被忽略。
6. SMOVE 将 member 元素从 source 集合移动到 destination 集合。
7. SCARD 返回集合 key 的基数(集合中元素的数量)。
8. SMEMBERS 返回集合 key 中的所有成员。
9. SSCAN 
10. SINTER 返回一个集合的全部成员，该集合是所有给定集合的交集。
11. SINTERSTORE 这个命令类似于 SINTER key [key …] 命令，但它将结果保存到 destination 集合，而不是简单地返回结果集。
12. SUNION 返回一个集合的全部成员，该集合是所有给定集合的并集。
13. SUNIONSTORE 这个命令类似于 SUNION key [key …] 命令，但它将结果保存到 destination 集合，而不是简单地返回结果集。
14. SDIFF 返回一个集合的全部成员，该集合是所有给定集合之间的差集。
15. SDIFFSTORE 这个命令的作用和 SDIFF key [key …] 类似，但它将结果保存到 destination 集合，而不是简单地返回结果集。

##### 1.2.4.2. 应用场景

1. 抽奖;
2. 随机场景；
3. 推荐系统：好友推荐,商品推荐；
4. 交并差集，数量不大，可以用。可以redis多实例。

##### 1.2.4.3. 实现方式

#### 1.2.5. sorted set

1. 动态维护排序。
2. 如何实现排序:ziplist(压缩表)和skiplist（跳跃表，value大了（大于64bytes）或元素个数大了(128)）。
3. skiplist:有一个层的概念，是一种随机化的数据。跳跃表以有序的方式在层次化的链表中保存元素， 效率和平衡树媲美 —— 查找、删除、添加等操作都可以在对数期望时间下完成， 并且比起平衡树来说， 跳跃表的实现要简单直观得多。
4. 跳跃表造层是一个随机的过程。

##### 1.2.5.1. 常用命令

1. ZADD 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。
2. ZSCORE 返回有序集 key 中，成员 member 的 score 值。
3. ZINCRBY 为有序集 key 的成员 member 的 score 值加上增量 increment 。
4. ZCARD 返回有序集 key 的基数。
5. ZCOUNT 返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量。
6. ZRANGE 返回有序集 key 中，指定区间内的成员。
7. ZREVRANGE 返回有序集 key 中，指定区间内的成员。其中成员的位置按 score 值递减(从大到小)来排列。 具有相同 score 值的成员按字典序的逆序(reverse lexicographical order)排列。
8. ZRANGEBYSCORE 返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。
9. ZREVRANGEBYSCORE 返回有序集 key 中， score 值介于 max 和 min 之间(默认包括等于 max 或 min )的所有的成员。有序集成员按 score 值递减(从大到小)的次序排列。
10. ZRANK 返回有序集 key 中成员 member 的排名。其中有序集成员按 score 值递增(从小到大)顺序排列。
11. ZREVRANK 返回有序集 key 中成员 member 的排名。其中有序集成员按 score 值递减(从大到小)排序。
12. ZREM 移除有序集 key 中的一个或多个成员，不存在的成员将被忽略。
13. ZREMRANGEBYRANK 移除有序集 key 中，指定排名(rank)区间内的所有成员。
14. ZREMRANGEBYSCORE 移除有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。
15. ZRANGEBYLEX 当有序集合的所有成员都具有相同的分值时， 有序集合的元素会根据成员的字典序（lexicographical ordering）来进行排序， 而这个命令则可以返回给定的有序集合键 key 中， 值介于 min 和 max 之间的成员。
16. ZLEXCOUNT 对于一个所有成员的分值都相同的有序集合键 key 来说， 这个命令会返回该集合中， 成员介于 min 和 max 范围内的元素数量。
17. ZREMRANGEBYLEX 对于一个所有成员的分值都相同的有序集合键 key 来说， 这个命令会移除该集合中， 成员介于 min 和 max 范围内的所有元素。
18. ZSCAN 
19. ZUNIONSTORE 计算给定的一个或多个有序集的并集，其中给定 key 的数量必须以 numkeys 参数指定，并将该并集(结果集)储存到 destination 。
20. ZINTERSTORE 计算给定的一个或多个有序集的交集，其中给定 key 的数量必须以 numkeys 参数指定，并将该交集(结果集)储存到 destination 。

##### 1.2.5.2. 应用场景

1. 分页
2. 排行榜。

##### 1.2.5.3. 实现方式

### 1.3. db

1. redis默认有16个db.

### 1.4. 全局命令

详见[Redis 命令参考](http://redisdoc.com/)
1. FLUSHALL
2. TYPE  查询key类型

#### 1.4.1. 事务

1. MULTI 标记一个事务块的开始。事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令原子性(atomic)地执行。
2. EXEC 执行所有事务块内的命令。假如某个(或某些) key 正处于 WATCH 命令的监视之下，且事务块中有和这个(或这些) key 相关的命令，那么 EXEC 命令只在这个(或这些) key 没有被其他命令所改动的情况下执行并生效，否则该事务被打断(abort)。
3. DISCARD 取消事务，放弃执行事务块内的所有命令。如果正在使用 WATCH 命令监视某个(或某些) key，那么取消所有监视，等同于执行命令 UNWATCH 。
4. WATCH 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。
5. UNWATCH  取消 WATCH 命令对所有 key 的监视。如果在执行 WATCH 命令之后， EXEC 命令或 DISCARD 命令先被执行了的话，那么就不需要再执行 UNWATCH 了。因为 EXEC 命令会执行事务，因此 WATCH 命令的效果已经产生了；而 DISCARD 命令在取消事务的同时也会取消所有对 key 的监视，因此这两个命令执行之后，就没有必要执行 UNWATCH 了。

#### 1.4.2. 调试

1. PING
2. ECHO
3. OBJECT OBJECT 命令允许从内部察看给定 key 的 Redis 对象.
4. SLOWLOG
5. MONITOR
6. DEBUG_OBJECT
7. DEBUG_SEGFAULT

#### 1.4.3. 持久化

1. SAVE
2. BGSAVE
3. BGREWRITEAOF
4. LASTSAVE

### 1.5. 持久化

1. 快照具有时间窗口，全量，加载快，rdb，缺点:容易丢失大部分数据；
2. 日志是实时的，记录比较完整，每一个操作都会追加到日志文件中，aof,缺点:加载慢和有冗余-（重写），拉低redis性能。
3. 4.x之后可以混合使用,aof文件包含rdb二进制和aof明文.
4. 并发很大，单实例有两个弊端:单点故障（主备，一变多）和性能瓶颈（分片集群）.

## 2. 配置

### 2.1. 配置文件 redis.conf

1. `优先设置maxmemory及maxmemory_policy回收策略`。
2. 注意：所有配置写进配置文件中，`redis-server`后边紧跟配置文件，不再缀参数。

```conf
protected_mode no  // 只能本地访问（127.0.0.1）不能通过（192.168**）访问
bind x.x.x.x    // 绑定访问的地址，如果不指定需要注释掉，配合上个参数使用
daemonize no    // docker中运行，守护模式为no
save ""    // 关闭rdb快照

```

### 2.2. 使用`config`命令

## 3. 单机版

1. `docker exec -it 43f7a65ec7f8 redis-cli`使用redis-cli连接到redis容器。
2. 生成带密码和持久化的容器：

```yml
redis:
    restart: always
    image: redis:5.0.5-alpine
    command: redis-server --requirepass zeHnzQ2uUOUAsKMk --appendonly yes
    volumes:
    - $PWD/data:/data
```

## 4. 集群(高可用、高并发)

1. 参考[docker-compose组建带密码redis集群 完整版](https://blog.csdn.net/alinyua/article/details/80936940).
2. Redis Sentinal着眼于高可用，在master宕机时会自动将slave提升为master，继续提供服务。
3. Redis Cluster着眼于扩展性，在单个redis内存不足时，使用Cluster进行分片存储。

### 4.1. 扩展

1. AKF扩展立方体（Scalability Cube)：这个立方体有三个轴线，每个轴线描述扩展性的一个维度，他们分别是产品、流程和团队：X轴 —— 代表无差别的克隆服务和数据，工作可以很均匀的分散在不同的服务实例上；Y轴 —— 关注应用中职责的划分，比如数据类型，交易执行类型的划分；Z轴 —— 关注服务和数据的优先级划分，如分地域划分。
2. CAP概念:可用性、一致性和分区容错。
3. 大多数分布式系统都分布在多个子网络。每个子网络就叫做一个区（partition）。分区容错的意思是，区间通信可能失败。比如，一台服务器放在中国，另一台服务器放在美国，这就是两个区，它们之间可能无法通信。
4. 一般来说，分区容错无法避免，因此可以认为 CAP 的 P 总是成立。CAP 定理告诉我们，剩下的 C 和 A 无法同时做到。
5. 一致性: 强一致性、弱一致性、最终一致性(黑盒订阅)。
6. 可用性。
7. 世界上只有一种一致性算法，就是 Paxos。出自一位 Google 大神之口。Paxos 也是出名的晦涩难懂，推理过程极其复杂。
8. [分布式理论(五) - 一致性算法Paxos](https://juejin.im/post/5b2664bd51882574874d8a76)。
9. 过半通过一致性。

## 5. 客户端

### 5.1. redis-cli

参考[redis cli命令](https://www.cnblogs.com/kongzhongqijing/p/6867960.html)

| 连接时参数 | 功能                      |
| ---------- | ------------------------- |
| -h         | 指定主机                  |
| -p         | 指定端口                  |
| -a         | 指定Auth password         |
| -r         | 代表将命令重复执行多次    |
| -i         | 每隔几秒,需要和-r一起使用 |
| --latency  | 可以看到延迟时间          |

| 命令   | 功能                                         |
| ------ | -------------------------------------------- |
| info   | 获得状态信息                                 |
| select | 切换到指定数据库                             |
| keys   | 返回满足pattern的所有key，可以使用正则表达式 |

#### 5.1.1. info命令

1. 获取的数据分为10个类别。

##### 5.1.1.1. server

##### 5.1.1.2. clients

1. 查看到当前实例的所有客户端连接信息

##### 5.1.1.3. `memory`比较重要

5个关键指标

###### 5.1.1.3.1. 内存使用率used_memory和used_memory_human

1. used_memory是Redis使用的内存总量，它包含了实际缓存占用的内存和Redis自身运行所占用的内存(如元数据、lua)。它是由Redis使用内存分配器分配的内存，所以这个数据并没有把内存碎片浪费掉的内存给统计进去。
2. 如果used_memory>可用最大内存，那就说明Redis实例正在进行内存交换或者已经内存交换完毕。

###### 5.1.1.3.2. used_memory_rss从操作系统上显示已经分配的内存总量

###### 5.1.1.3.3. mem_fragmentation_ratio内存碎片率

1. used_memory_rss的rss是Resident Set Size的缩写，表示该进程所占物理内存的大小，是操作系统分配给Redis实例的内存大小。除了用户定义的数据和内部开销以外，used_memory_rss指标还包含了内存碎片的开销，内存碎片是由操作系统低效的分配/回收物理内存导致的。
2. `内存碎片率稍大于1是合理的，这个值表示内存碎片率比较低，也说明redis没有发生内存交换。但如果内存碎片率超过1.5，那就说明Redis消耗了实际需要物理内存的150%，其中50%是内存碎片率。若是内存碎片率低于1的话，说明Redis内存分配超出了物理内存，操作系统正在进行内存交换。内存交换会引起非常明显的响应延迟`.

###### 5.1.1.3.4. used_memory_lua Lua脚本引擎所使用的内存大小。

mem_allocator： 在编译时指定的Redis使用的内存分配器，可以是libc、jemalloc、tcmalloc。

##### 5.1.1.4. persistence

##### 5.1.1.5. `stats`比较重要

###### 5.1.1.5.1. 命令处理总数total_commands_processed

1. 通过记录的total_commands_processed历史数据值来判断命理处理总数是上升趋势还是下降趋势，以便排查问题。

##### 5.1.1.6. replication

##### 5.1.1.7. cpu

##### 5.1.1.8. commandstats

##### 5.1.1.9. cluster

##### 5.1.1.10. keyspace

#### 5.1.2. config命令

1. config rewrite命令会把内存中的新配置刷新到配置文件(redis-server 必须指定配置文件启动)中。

#### 5.1.3. 清除数据

1. `flushall`
2. `flushdb`

#### 5.1.4. 命令方式

1. `redis-cli ping`测试连接能否成功。

#### 5.1.5. 交互式方式

1. `redis-cli`连接成功之后进入交互式方式。

#### 5.1.6. 批量操作

1. 批量删除key（使用xargs命令） `./redis-cli -h redis所在服务器ip -p 端口 keys "course-*" | xargs ./redis-cli -h redis所在服务器ip -p 端口 del`

### 5.2. [Redis Desktop Manager](https://redisdesktop.com/)

桌面工具

`sudo snap install redis-desktop-manager`

### 5.3. [erikdubbelboer/phpRedisAdmin](https://github.com/ErikDubbelboer/phpRedisAdmin)

网页端工具

Simple web interface to manage Redis databases.

## 6. 使用见后端框架文档。

## 7. 使用原则及警告(使用规范)

1. 每个DB中可以有多个文件夹（可递归，使用':'隔开）。
2. `警告`多线程访问同一个redis资源时，会出先共享资源冲突的问题，由其修改其中的值。
3. 解决2的问题，有几种思路:(1)[通过watch（监控）+mutil（事务）实现应用于在分布式高并发处理等相关场景](https://www.cnblogs.com/yy3b2007com/p/9383713.html#autoid-2-4-0)解决读取和修改的原子操作。同时修改(set等)会中断。(2)使用锁(3)不修改。

### 7.1. 键值设计

#### 7.1.1. key名设计

1. 以业务名(或数据库名)为前缀(防止key冲突)，`用冒号分隔`，比如业务名:表名:id；
2. 保证语义的前提下，控制key的长度，当key较多时，内存占用也不容忽视；
3. 不要包含特殊字符。

#### 7.1.2. value设计

1. 【拒绝bigkey】防止网卡流量、慢查询，string类型控制在10KB以内，hash、list、set、zset元素个数不要超过5000;
2. 非字符串的bigkey，不要使用del删除，使用hscan、sscan、zscan方式渐进式删除，同时要注意防止bigkey过期时间自动删除问题(例如一个200万的zset设置1小时过期，会触发del操作，造成阻塞，而且该操作不会不出现在慢查询中(latency可查)).
3. 【选择适合的数据类型】
4. 【控制key的生命周期】redis不是垃圾桶，建议使用expire设置过期时间(条件允许可以打散过期时间，防止集中过期)，不过期的数据重点关注idletime。

### 7.2. 命令使用

1. 【O(N)命令关注N的数量】例如hgetall、lrange、smembers、zrange、sinter等并非不能使用，但是需要明确N的值。有遍历的需求可以使用hscan、sscan、zscan代替。
2. 【禁用命令】禁止线上使用keys、flushall、flushdb等，通过redis的rename机制禁掉命令，或者使用scan的方式渐进式处理。
3. 【合理使用select】redis的多数据库较弱，使用数字进行区分，很多客户端支持较差，同时多业务用多数据库实际还是单线程处理，会有干扰。
4. 【使用批量操作提高效率】1.原生命令：例如mget、mset。2.非原生命令：可以使用pipeline提高效率。但要注意控制一次批量操作的元素个数(例如500以内，实际也和元素字节数有关)。注意两者不同：原生是原子操作，pipeline是非原子操作，pipeline可以打包不同的命令，原生做不到，pipeline需要客户端和服务端同时支持。
5. 【不建议过多使用Redis事务功能】Redis的事务功能较弱(不支持回滚)，而且集群版本(自研和官方)要求一次事务操作的key必须在一个slot上(可以使用hashtag功能解决)。
6. 【Redis集群版本在使用Lua上有特殊要求】
7. 【monitor命令】必要情况下使用monitor命令时，要注意不要长时间使用。

### 7.3. 客户端使用

1. 避免多个应用使用一个Redis实例：不相干的业务拆分，公共数据做服务化。
2. 使用连接池：可以有效控制连接，同时提高效率，标准使用方式。

    ```java
    Jedis jedis = null;
    try {
        jedis = jedisPool.getResource();
        //具体的命令
        jedis.executeCommand()
    } catch (Exception e) {
        logger.error("op key {} error: " + e.getMessage(), key, e);
    } finally {
        //注意这里不是关闭连接，在JedisPool模式下，Jedis会被归还给资源池。
        if (jedis != null) 
            jedis.close();
    }
    ```

3. 【熔断功能】高并发下建议客户端添加熔断功能(例如netflix hystrix)。
4. 【合理的加密】设置合理的密码，如有必要可以使用SSL加密访问（阿里云Redis支持）。
5. 【淘汰策略】根据自身业务类型，选好maxmemory-policy(最大内存淘汰策略)，设置好过期时间。

## 8. **性能指标及优化**

### 8.1. [redis-benchmark](https://redis.io/topics/benchmarks)压测

### 8.2. 通过减少Redis的内存占用率，来避免这样的问题，或者使用下面的技巧来避免内存交换发生

#### 8.2.1. 可以选择使用32位

1. 假如缓存数据小于4GB，就使用32位的Redis实例。因为32位实例上的指针大小只有64位的一半，它的内存空间占用空间会更少些。 这有一个坏处就是，假设物理内存超过4GB，那么32位实例能使用的内存仍然会被限制在4GB以下。 要是实例同时也共享给其他一些应用使用的话，那可能需要更高效的64位Redis实例，这种情况下切换到32位是不可取的。 不管使用哪种方式，Redis的dump文件在32位和64位之间是互相兼容的， 因此倘若有减少占用内存空间的需求，可以尝试先使用32位，后面再切换到64位上。

#### 8.2.2. 使用Hash数据结构

1. `尽可能的使用Hash数据结构。因为Redis在储存小于100个字段的Hash结构上，其存储效率是非常高的`。所以在不需要集合(set)操作或list的push/pop操作的时候，尽可能的使用Hash结构。比如，在一个web应用程序中，需要存储一个对象表示用户信息，使用单个key表示一个用户，其每个属性存储在Hash的字段里，这样要比给每个属性单独设置一个key-value要高效的多。 通常情况下倘若有数据使用string结构，用多个key存储时，那么应该转换成单key多字段的Hash结构。 如上述例子中介绍的Hash结构应包含，单个对象的属性或者单个用户各种各样的资料。Hash结构的操作命令是HSET(key, fields, value)和HGET(key, field)，使用它可以存储或从Hash中取出指定的字段。

#### 8.2.3. 设置过期时间

1. `设置key的过期时间`。一个减少内存使用率的简单方法就是，每当存储对象时确保设置key的过期时间。倘若key在明确的时间周期内使用或者旧key不大可能被使用时，就可以用Redis过期时间命令(expire,expireat, pexpire, pexpireat)去设置过期时间，这样Redis会在key过期时自动删除key。 假如你知道每秒钟有多少个新key-value被创建，那可以调整key的存活时间，并指定阀值去限制Redis使用的最大内存。

#### 8.2.4. 回收KEY

1. `回收key`。在Redis配置文件中(一般叫Redis.conf)，通过设置“maxmemory”属性的值可以限制Redis最大使用的内存，修改后重启实例生效。 也可以使用客户端命令`config set maxmemory` 去修改值，这个命令是立即生效的，但会在重启后会失效，需要使用config rewrite命令去刷新配置文件。 若是启用了Redis快照功能，应该设置“maxmemory”值为系统可使用内存的45%，因为快照时需要一倍的内存来复制整个数据集，也就是说如果当前已使用45%，在快照期间会变成95%(45%+45%+5%)，其中5%是预留给其他的开销。 如果没开启快照功能，maxmemory最高能设置为系统可用内存的95%。
2. 选择一种key的回收策略，可在Redis.conf配置文件中修改`maxmemory-policy`属性值。 若是Redis数据集中的key都设置了过期时间，那么“volatile-ttl”策略是比较好的选择。但如果key在达到最大内存限制时没能够迅速过期，或者根本没有设置过期时间。那么设置为“allkeys-lru”值比较合适，它允许Redis从整个数据集中挑选最近最少使用的key进行删除(LRU淘汰算法)。Redis还提供了一些其他淘汰策略，如下：
a. volatile-lru：使用LRU算法从已设置过期时间的数据集合中淘汰数据。
b. volatile-ttl：从已设置过期时间的数据集合中挑选即将过期的数据淘汰。
c. volatile-random：从已设置过期时间的数据集合中随机挑选数据淘汰。
d. allkeys-lru：使用LRU算法从所有数据集合中淘汰数据。
e. allkeys-random：从数据集合中任意选择数据淘汰
f. no-enviction：禁止淘汰数据。
3. 通过设置maxmemory为系统可用内存的45%或95%(取决于持久化策略)和设置“maxmemory-policy”为“volatile-ttl”或“allkeys-lru”(取决于过期设置)，可以比较准确的限制Redis最大内存使用率，在绝大多数场景下使用这2种方式可确保Redis不会进行内存交换。倘若你担心由于限制了内存使用率导致丢失数据的话，可以设置noneviction值禁止淘汰数据。

### 8.3. 使用命令处理总数解决延迟时间增加

1. 通过与记录的历史数据比较得知，命令处理总数确实是处于上升或下降状态，那么可能是有2个原因引起的:
a. 命令队列里的命令数量过多，后面命令一直在等待中。
b. 几个慢命令阻塞Redis。
2. 有三个办法可以解决

#### 8.3.1. 使用多参数命令

1. 单命令多参数的形式取代多命令单参数的形式。
2. 举例来说，循环使用LSET命令去添加1000个元素到list结构中，是性能比较差的一种方式，更好的做法是在客户端创建一个1000元素的列表，用单个命令LPUSH或RPUSH，通过多参数构造形式一次性把1000个元素发送的Redis服务上。

#### 8.3.2. 管道命令

1. 另一个减少多命令的方法是使用管道(pipeline)，把几个命令合并一起执行，从而减少因网络开销引起的延迟问题。

#### 8.3.3. 避免操作大集合的慢命令

1. 如果命令处理频率过低导致延迟时间增加，这可能是因为使用了高时间复杂度的命令操作导致。
2. 减少使用高时间复杂的命令，能显著的提高的Redis的性能。

### 8.4. 跟踪延迟时间提高性能

1. 拿1G带宽来说，若是延迟时间远高于200μs，那明显是出现了性能问题。

#### 8.4.1. 使用`slowlog`命令查出引发延迟的慢命令

#### 8.4.2. 监控客户端的连接

#### 8.4.3. 限制客户端连接数

1. 在配置文件(Redis.conf)maxclients属性上修改客户端连接的最大数。
2. 在Redis-cli工具上输入config set maxclients 去设置最大连接数。
3. 根据连接数负载的情况，这个数字应该设置为预期连接数峰值的110%到150之间，若是连接数超出这个数字后，Redis会拒绝并立刻关闭新来的连接。

#### 8.4.4. 加强内存管理

#### 8.4.5. 性能数据指标

1. 对于这种性能指标相关联的分析，需要从历史数据上来观察到数据指标的重要变化，此外还可以观察到单个性能指标相关联的所有其他性能指标信息。这些数据可以在Redis上收集，周期性的调用内容为Redis info的脚本，然后分析输出的信息，记录到日志文件中。当延迟发生变化时，用日志文件配合其他数据指标，把数据串联起来排查定位问题。

### 8.5. 用内存碎片率预测性能问题

#### 8.5.1. 重启Redis服务器

1. 如果内存碎片率超过1.5，重启Redis服务器可以让额外产生的内存碎片失效并重新作为新内存来使用，使操作系统恢复高效的内存管理。
2. 通过比较used_memory_peak, used_memory_rss和used_memory_metrics的数据指标值可以检查额外内存碎片的占用。从名字上可以看出，used_memory_peak是过去Redis内存使用的峰值，而不是当前使用内存的值。如果used_memory_peak和used_memory_rss的值大致上相等，而且二者明显超过了used_memory值，这说明额外的内存碎片正在产生。
3. 在重启服务器之前，需要在Redis-cli工具上输入shutdown save命令，意思是强制让Redis数据库执行保存操作并关闭Redis服务，这样做能保证在执行Redis关闭时不丢失任何数据。 在重启后，Redis会从硬盘上加载持久化的文件，以确保数据集持续可用。

#### 8.5.2. 限制内存交换

1. 如果内存碎片率低于1，Redis实例可能会把部分数据交换到硬盘上。内存交换会严重影响Redis的性能，所以应该增加可用物理内存或减少实Redis内存占用。

#### 8.5.3. 修改内存分配器

1. Redis支持glibc’s malloc、jemalloc11、tcmalloc几种不同的内存分配器，每个分配器在内存分配和碎片上都有不同的实现。不建议普通管理员修改Redis默认内存分配器，因为这需要完全理解这几种内存分配器的差异，也要重新编译Redis。

### 8.6. 减少回收key以提升性能

1. 跟踪key回收是非常重要的，因为通过回收key，可以保证合理分配Redis有限的内存资源。如果evicted_keys值经常超过0，那应该会看到客户端命令响应延迟时间增加，因为Redis不但要处理客户端过来的命令请求，还要频繁的回收满足条件的key。
2. 回收key对性能的影响远没有内存交换严重，若是在强制内存交换和设置回收策略做一个选择的话，选择设置回收策略是比较合理的，因为把内存数据交换到硬盘上对性能影响非常大.

#### 8.6.1. 增加内存限制

#### 8.6.2. 对实例进行分片

1. 下面是Redis实现分片的几种常见方式：
a. Hash分片：一个比较简单的方法实现，通过Hash函数计算出key的Hash值，然后值所在范围对应特定的Redis实例。
b. 代理分片：客户端把请求发送到代理上，代理通过分片配置表选择对应的Redis实例。 如Twitter的Twemproxy，豌豆荚的codis。
c. 一致性Hash分片
d. 虚拟桶分片

