# 产品设计-SAAS


# IaaS PaaS SaaS

1. 参考[IaaS，PaaS，SaaS 的区别](http://www.ruanyifeng.com/blog/2017/07/iaas-paas-saas.html).
2. SaaS 模式下用户没有任何自主权，只能使用给定的应用程序；PaaS 模式下可以自己安装应用程序，但是不能定制操作系统；IaaS 模式下则是云服务商提供（虚拟的）硬件，从操作系统开始都可以自己选择和定制。
3. [ripienaar/free-for-dev](https://github.com/ripienaar/free-for-dev)A list of SaaS, PaaS and IaaS offerings that have free tiers of interest to devops and infradev.

# 单租户和多租户

摘自[单租户&多租户学习笔记](https://blog.csdn.net/hnust_gebilaowang/article/details/53725359)

## 单租户SaaS架构

1. 被称作多实例架构，指的是为每个客户单独创建各自的软件应用和支撑环境。单租户SaaS被广泛引用在客户需要支持定制化的应用场合。
2. 每个客户都有一份分别放在独立的服务器上的数据库和操作系统，或者使用强的安全措施进行隔离的虚拟网络环境中。

## 多租户SaaS架构

1. 应用都是运行在同样的一个或者一组服务器上，被称为“单实例”架构（Single Instance）。
2. 多租户比较适合通用类需求的客户，即不需要对主线功能进行调整或者重新配置的客户。多个租户的数据是保存在相同的位置，并且通常是依靠分区来确保租户之间的数据隔离。
3. 多租户可以分为几个不同的类别：
    a) 云中的简单虚拟化，其中只对硬件进行共享。它提供了最强的分离度，租户的数据彼此物理不可见，备份与恢复都很灵活，但是资源共享低，成本高。
    b) 共享应用程序，对每个租户使用不同的数据库。租户间数据彼此逻辑不可见，上层应用程序的实现和独立数据库一样简单，但是备份恢复稍显复杂.
    c) 共享应用程序和数据库（效率最高，真正的多租户）。租户数据在数据表级别实现共享，它提供了最低的成本，但是引入了额外的编程复杂性（程序的数据访问需要用 tenantId 来区分不同租户），备份与恢复也更复杂。

# 搭建SaaS平台

1. 参考[将您的 web 应用程序转化为多租户 SaaS 解决方案](https://www.ibm.com/developerworks/cn/cloud/library/cl-multitenantsaas/)

## 概念

[参考](https://www.cnblogs.com/thanks/p/10622082.html)

云自助服务平台，可能和网上购物、在线商城有些类似，但里面提供的是相关服务，还是有些区别的.

1. 产品：产品即服务，即是提供给用户的服务。产品有单价，有些产品是基础产品，用户购买正式产品必须免费提供的，产品可以提供给用户进行试用。
2. 模块：产品包括很多模块，有些模块是必然会提供给用户的，比如 操作人员管理、操作日志 等，还有些模块是可选的，用户针对自己的情况进行购买，类似增值服务，比如移动端、企业主页等。另外还有些一次性的服务，比如系统数据对接，硬件设备购买等；
3. 服务：用户所能享受到的服务，有一定的使用期限；
4. 订单：用户根据所拥有的 服务 所下的订单（而不是产品哦，为什么？）；
5. 购物车：在用户订单生成前先把产品放在购物车里，购物车有很多类别，有的购物车是对目前服务进行的延期，有些是把试用的产品转为正式，有些是对现有服务模块的增删，牵涉到追加购买等。购物车操作频繁、需要做非常多的校验，要和已经购买的服务做无缝的对接，这也是云SAAS产品和普通电商很大不同的地方。到了订单阶段，就相对比较简单了，生成订单后将购物车清空、可以生成多张订单，支付的时候再做一遍校验。

总体的概念流程是 模块->产品->购物车->订单->服务

## 开源软件

1. [forter/security-101-for-saas-startups](https://github.com/forter/security-101-for-saas-startups)
2. [chillzhuang/SpringBlade](https://github.com/chillzhuang/SpringBlade)SpringBlade 是一个由商业级项目升级优化而来的SpringCloud分布式微服务架构、SpringBoot单体式微服务架构并存的综合型项目，采用Java8 API重构了业务代码，完全遵循阿里巴巴编码规范。采用Spring Boot 2 、Spring Cloud Greenwich 、Mybatis 等核心技术，同时提供基于React和Vue的两个前端框架用于快速搭建企业级的SaaS多租户微服务平台。 官网：https://bladex.vip
3. [sqreen/CTOSecurityChecklist](https://github.com/sqreen/CTOSecurityChecklist).
4. [zuihou/zuihou-admin-cloud](https://github.com/zuihou/zuihou-admin-cloud)基于SpringCloud(Greenwich.RELEASE) + SpringBoot(2.1.2.RELEASE) 的SaaS 微服务脚手架，具有统一授权、认证后台管理系统，其中包含具备用户管理、资源权限管理、网关API、分布式事务、大文件断点分片续传等多个模块，支持多业务系统并行开发，可以作为后端服务的开发脚手架。代码简洁，架构清晰，适合学习和直接项目中使用。核心技术采用Eureka、Fegin、Ribbon、Zuul、Hystrix、JWT Token、Mybatis、SpringBoot、Redis、等主要框架和中间件。
5. [async-labs/saas](https://github.com/async-labs/saas)Build your own SaaS business with SaaS boilerplate. Productive stack: React, Material-UI, Next, MobX, WebSockets, Express, Node, Mongoose, MongoDB. Written with TypeScript.
6. [Atarity/deploy-your-own-saas](https://github.com/Atarity/deploy-your-own-saas)List of "only yours" cloud services for everyday needs
7. [fjb040911/Comb](https://github.com/fjb040911/Comb)
