# 微服务架构笔记-中间件-消息队列


## 注意

1. 消息队列的使用，按时间分块，实时计算（少量的批量计算流）。

## 声明

1. 参考[项目用了消息队列，不知道为啥用，有点尴尬](https://mp.weixin.qq.com/s/PG29Ig3ttBEznI7oG0nimw)

## 应用场景

### 优点

1. 解耦
2. 异步
3. 削峰

### 缺点

1. 系统可用性降低，增加了一层消息队列，就增加了一层风险。
2. 系统复杂性增加。

## 消息队列选型

|特性|ActiveMQ|RabbitMQ|RocketMQ|Kafka|
|--|--|--|--|--|
|单机吞吐量|万级，比RocketMQ\Kafka低一个数量级|同ActiveMQ|10万级，支持高吞吐|10万级，高吞吐，一般配合大数据类的系统来进行实时数据计算、日志采集等场景||
|topic数量对吞吐量的影响|||topic可以达到几百、几千的级别，吞吐量会有较小幅度的下降，这是RocketMQ的一大优势，在同等机器下，可以支撑大量的topic|topic从几十到几百个时候，吞吐量会大幅度下降，在同等机器下，Kafka尽量保证topic数量不要过多，如果支撑大规模的topic，需要增加更多的机器资源|
|时效性|ms级|微秒级，这是RabbitMQ的一大特点、延迟最低|ms级|延迟在ms级以内|
|可用性|高，基于主从架构实现高可用|同ActiveMQ|非常高，分布式架构|非常高，分布式，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用|
|消息可读性|有较低的概率丢失数据|基本不丢|经过参数优化配置，可以做到0丢失|同RocketMQ|
|功能支持|MQ领域的功能极其完备|基于erlang开发，并发能力很强，性能很好，延时很低|MQ功能较为完善，还是分布式的，扩展性好|功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用|
|活跃度|不太活跃|活跃|较活跃|较活跃|

* Topic是消息中间件里一个重要的概念，每一个Topic代表了一类消息，有了多个Topic，就可以对消息进行归类与隔离。
* Kafka和RocketMQ都是磁盘消息队列的模式，对于同一个消费组，一个分区只支持一个消费线程来消费消息。过少的分区，会导致消费速度大大落后于消息的生产速度。所以在实际生产环境中，一个Topic会设置成多分区的模式，来支持多个消费者.

### RabbitMQ    *****

1. 中小型软件公司，建议选RabbitMQ，
2. erlang语言天生具备高并发的特性，而且他的管理界面用起来十分方便。但是定制开发比较难。
3. RabbitMQ的社区十分活跃，可以解决开发过程中遇到的bug，这点对于中小型公司来说十分重要。
4. 中小型软件公司不如互联网公司，数据量没那么大，选消息中间件，应首选功能比较完备的，所以kafka排除。
5. rocketmq是阿里出品，如果阿里放弃维护rocketmq，中小型公司一般抽不出人来进行rocketmq的定制化开发，因此不推荐。

### RocketMQ

1. 大型软件公司，根据具体使用在rocketMq和kafka之间二选一。
2. 针对rocketMQ,大型软件公司也可以抽出人手对rocketMQ进行定制化开发，毕竟国内有能力改JAVA源码的人，还是相当多的。
3. 至于kafka，根据业务场景选择，如果有日志采集功能，肯定是首选kafka了。具体该选哪个，看使用场景。

## 如何保证消息队列是高可用的

1. 要对消息队列的集群模式有深刻了解。

### 以rcoketMQ为例，他的集群就有多master 模式、多master多slave异步复制模式、多 master多slave同步双写模式。

### rabbitMQ,也有普通集群和镜像集群模式

## 如何保证消息不被重复消费

1. 正常情况下，消费者在消费消息时候，消费完毕后，会发送一个确认信息给消息队列，消息队列就知道该消息被消费了，就会将该消息从消息队列中删除。只是不同的消息队列发送的确认信息形式不同。
2. 因为网络传输等等故障，确认信息没有传送到消息队列，导致消息队列不知道自己已经消费过该消息了，再次将该消息分发给其他的消费者。

### 解决方法

1. 使用第三方介质（redis），做消费记录。

## 如何保证消费的可靠性传输

### RabbitMQ

#### 生产者丢数据

1. RabbitMQ提供transaction和confirm模式来确保生产者不丢消息。
2. transaction机制就是说，发送消息前，开启事务(channel.txSelect())，然后发送消息，如果发送过程中出现什么异常，事务就会回滚(channel.txRollback())，如果发送成功则提交事务(channel.txCommit())。

#### 消息队列丢数据

1. 处理消息队列丢数据的情况，一般是开启持久化磁盘的配置。
2. 这个持久化配置可以和confirm机制配合使用，你可以在消息持久化磁盘后，再给生产者发送一个Ack信号。
3. 这样，如果消息持久化磁盘之前，rabbitMQ阵亡了，那么生产者收不到Ack信号，生产者会自动重发。

#### 消费者丢数据

1. 消费者丢数据一般是因为采用了自动确认消息模式。
2. 这种模式下，消费者会自动确认收到信息。这时rahbitMQ会立即将消息删除，这种情况下如果消费者出现异常而没能处理该消息，就会丢失该消息。
3. 至于解决方案，采用手动确认消息即可。


## 如何保证消息队列的顺序性

通过某种算法，将需要保持先后顺序的消息放到同一个消息队列中(kafka中就是partition,rabbitMq中就是queue)。然后只用一个消费者去消费该队列。



