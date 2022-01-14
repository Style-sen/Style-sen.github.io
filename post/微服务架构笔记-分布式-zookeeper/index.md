# 微服务架构笔记-分布式-ZooKeeper


参考[阿里巴巴为什么不用 ZooKeeper 做服务发现？](https://mp.weixin.qq.com/s/HTZcM-6NIOfxLunvdj2yaQ)

# 优点
1. 阿里巴巴中间件内部也维护了一个面向大规模生产的、高可用、更易监控和运维的 ZooKeeper 的代码分支 TaoKeeper，如果以我们近 10 年在各个业务线和生产上使用 ZooKeeper 的实践，给 ZooKeeper 用一个短语评价的话，那么我们认为 ZooKeeper 应该是 “The King Of Coordination for Big Data”！
2. 可以使用 ZooKeeper，但是大数据请向左，而交易则向右，分布式协调向左，服务发现向右。


# 缺点

