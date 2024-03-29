# 分布式-概念（与微服务的关系）


## 与微服务

1. 服务化的本质是“分治”（服务拆分、服务治理）。
2. 服务化的模式比如SOA、ESB、微服务等。
3. 高内聚、低耦合。

## 与中间件

1. 中间件起到的是标准化的作用；
2. 中间件只是起到承载这些标准化想法的介质、工具，可以起到引导和约束的效果，以此起到大大降低系统复杂度和写作成本的作用；
3. MQ框架标准化了不同应用程序间非实时异步通讯的方式；
4. RPC框架标准化了不同应用程序间实时通讯的方式；
5. DAL（Data Access Layer，数据访问层）框架标准化了应用程序和数据库之间通讯的方式。
6. 识别相对稳定的部分是什么，如何把他们提炼出来，并且围绕这些点进行标准化，才是我们需要掌握的，建立中间件的能力。

## 分布式系统包含前两者（涉及多个进程协作才能提供一个完整功能的系统都是）
