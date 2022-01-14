# 产品设计-单点登录


[关联](https://style-sen.github.io/2019/01/21/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%AC%94%E8%AE%B0-1-%E8%AE%A4%E8%AF%81%E5%92%8C%E6%8E%88%E6%9D%83/)

# 目的

## 第三方应用登录

1. 例如GITHUB`OAuth applications are used to access the GitHub API.`,在github注册一个OAuth APP(拿到key和secret)就可以让用户使用github账号登录本网站，本网站可以获取此用户在github上的授权信息。

## 一处登录，处处登录；一处注销，处处注销

1. 不同域名下，一处登录，处处登录，一处注销，处处注销。

### 单点登录的实现方案(非前后端分离)
一般就包含以下三种：
1. Cookies
2. Session同步
3. 分布式Session方式

#### Cookies
缺点：
1. 不安全;
2. 不能跨域实现免登。
#### Session同步
#### 分布式Session
流程运行：
1. 用户第一次登录时，将会话信息（用户Id和用户信息），比如以用户Id为Key，写入分布式Session； 
2. 用户再次登录时，获取分布式Session，是否有会话信息，如果没有则跳到登录页；
3. 一般采用缓存中间件实现，建议使用Redis，因此它有持久化功能，方便分布式Session宕机后，可以从持久化存储中加载会话信息；
4. 存入会话时，可以设置会话保持的时间，比如15分钟，超过后自动超时.

### 常见方案

要点有两个：存储信任和验证信任。

#### 身份认证技术
##### cas（单点登录）
1. 缺点：cas单点登录技术适用于传统应用的场景比较多， 官方示例也是以javaWeb为准， `对微服务化应用，前后端分离应用，支持性较差`。
##### Spring Security OAuth2（第三方登录授权：github登陆）
1. OAuth2是用来允许用户授权第三方应用访问他在另一个服务器上的资源的一种协议，它不是用来做单点登录的，但我们可以利用它来实现单点登录。
##### jwt (客户端token：原生)
#### 安全控制框架
##### spring-security
##### shiro


### 解决的问题

1. session共享的问题：
    （1）负载均衡（iphash），但是如果单点故障，还是需要重新登录。
    （2）session共享，但是集群数量大的话，会造成效率缓慢。
2. 方法：使用第三方redis，保存用户信息（UUID：token）。token保存在cookie中。

# 实现

以Spring Boot或者以SSM为基础。

# 知识点

1. [cookie、session、sessionid与jsessionid](https://www.cnblogs.com/fnng/archive/2012/08/14/2637279.html)

## JSONP
1. [JSONP原理及实现跨域方式](https://segmentfault.com/a/1190000002799156)

# 难点

1. 不同域名如何实现同时登录？
a.利用localStorage实现跨域通信。目前广泛采用的是postMessage和iframe相结合的方法。postMessage(data,origin)方法允许来自不同源的脚本采用异步方式进行通信，可以实现跨文本档、多窗口、跨域消息传递。[localstorage的跨域存储方案](https://www.jianshu.com/p/e86d92aeae69).
b. [localStorage和cookie的跨域解决方案](https://www.haorooms.com/post/kuayu_localstorage_cookie).
2. 淘宝天猫的cookie同步问题？
a. [聊聊阿里淘宝SSO跨域登录过程](https://youngzhang08.github.io/2018/08/08/%E8%81%8A%E8%81%8A%E9%98%BF%E9%87%8C%E6%B7%98%E5%AE%9DSSO%E8%B7%A8%E5%9F%9F%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B/)
