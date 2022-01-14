# 微服务架构笔记(1)认证和授权


解决问题：微服务之间以及你的应用和微服务之间是怎么信任对方的？
参考[理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
参考[OAuth2.0 原理流程及其单点登录和权限控制](https://kefeng.wang/2018/04/06/oauth2-sso/)

## 概念

1. 认证(authentication)和授权(authorization)。这是两个不同的概念，通俗点讲，认证是指系统需要确认你是谁，而授权是指在通过认证之后，你能干什么。
2. 第三方应用程序(Third-party application):即需要访问用户在HTTP服务中的资源,下文又称'客户端'；
3. HTTP服务提供商(HTTP service);
4. 资源所有者(Resource Owner):即HTTP服务的用户;
5. 用户代理(User Agent):例浏览器；
6. 认证服务器(Authorization server):即服务提供商专门用来处理认证的服务器;
7. 资源服务器(Resource server):即服务提供商存放用户生成的资源的服务器;它与认证服务器，可以是同一台服务器，也可以是不同的服务器.

## 认证

### Http Basic认证

1. 将用户名密码按照格式"用户名:密码"通过Base-64编码成一个hash值，然后通过Authorization header传递到服务端，然后服务端再通过同样的Base-64编码方式进行解码成为"用户名:密码"格式进行认证。
2. 容易被破解，结合Https使用。

### Http Digest认证

### Cookies & Session

1. 一般采用Token。

## 授权

在微服务架构中，一般都需要有自己的一个授权服务器，它的作用主要是分发token给不同的调用方，然后它们可以使用这个token去访问相应的微服务。

可以把授权服务器看成是提供一组REST API的service：

1. 授权API(/oauth/authorize) - 这个API会对调用方请求进行授权，返回一个authorization code。
2. 获取Token API(/oauth/token) - 这个API会根据客户请求传入的authorization code来生成一个access token并返回。
3. 校验Token API(/oauth/introspect) - 这个API一般会是resource server用来校验请求方的access token是否有效。
4. 撤销Token API(/oauth/revoke) - 这个API 会把access token直接撤销。


## 单点登录

1. 单点登录是多域名企业站点流行的登录方式。
2. 传统的多点登录系统中，每个站点都实现了本站专用的帐号数据库和登录模块。各站点的登录状态相互不认可，各站点需要逐一手工登录。
3. 单点登录，英文是 Single Sign On，缩写为 SSO。多个站点共用一台认证授权服务器(用户数据库和认证授权模块共用)。用户经由其中任何一个站点登录后，可以免登录访问其他所有站点。而且，各站点间可以通过该登录状态直接交互。

## OAuth2

1. OAuth的作用既可以满足微服务架构中服务的统一认证和授权，也可以让"第三方应用程序"安全可控地获取"资源所有者"的授权，与"服务商提供商"进行互动。
2. OAuth在"客户端"与"服务提供商"之间，设置了一个授权层（authorization layer）。"客户端"不能直接登录"服务提供商"，只能登录授权层，以此将用户与客户端区分开来。"客户端"登录授权层所用的令牌（token），与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。"客户端"登录授权层以后，"服务提供商"根据令牌的权限范围和有效期，向"客户端"开放用户储存的资料。
3. *资源服务器也要会判断认证服务器给的Token的有效性，可以通过共享数据库*
4. [OAuth2中文文档](https://github.com/jeansfish/RFC6749.zh-cn/blob/master/SUMMARY.md)


### 流程

A. 用户打开客户端以后，客户端要求用户给予授权。
B. 用户同意给予客户端授权。
C. 客户端使用上一步获得的授权，向认证服务器申请令牌。
D. 认证服务器对客户端进行认证以后，确认无误，同意发放令牌。
E. 客户端使用令牌，向资源服务器申请获取资源。
F. 资源服务器确认令牌无误，同意向客户端开放资源。

### 授权方式

#### 授权码方式

授权码方式（authorization code）是功能最完整、流程最严密的授权模式。它的特点就是通过客户端的后台服务器，与"服务提供商"的认证服务器进行互动。

（A）用户访问客户端，后者将前者导向认证服务器。
（B）用户选择是否给予客户端授权。
（C）假设用户给予授权，认证服务器将用户导向客户端事先指定的"重定向URI"（redirection URI），同时附上一个授权码。
（D）客户端收到授权码，附上早先的"重定向URI"，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。
（E）认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。

#### 简化方式

简化模式（implicit grant type）不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，跳过了"授权码"这个步骤，因此得名。所有步骤在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证。

（A）客户端将用户导向认证服务器。
（B）用户决定是否给于客户端授权。
（C）假设用户给予授权，认证服务器将用户导向客户端指定的"重定向URI"，并在URI的Hash部分包含了访问令牌。
（D）浏览器向资源服务器发出请求，其中不包括上一步收到的Hash值。
（E）资源服务器返回一个网页，其中包含的代码可以获取Hash值中的令牌。
（F）浏览器执行上一步获得的脚本，提取出令牌。
（G）浏览器将令牌发给客户端。

用在移动app或者web app(这些app是在用户的设备上的，如在手机上调起微信来进行认证授权)。

#### 密码模式

密码模式（Resource Owner Password Credentials Grant）中，用户向客户端提供自己的用户名和密码。客户端使用这些信息，向"服务商提供商"索要授权。
在这种模式中，用户必须把自己的密码给客户端，但是客户端不得储存密码。这通常用在用户对客户端高度信任的情况下，比如客户端是操作系统的一部分，或者由一个著名公司出品。而认证服务器只有在其他授权模式无法执行的情况下，才能考虑使用这种模式。

（A）用户向客户端提供用户名和密码。
（B）客户端将用户名和密码发给认证服务器，向后者请求令牌。
（C）认证服务器确认无误后，向客户端提供访问令牌。

#### 客户端模式

客户端模式（Client Credentials Grant）指客户端以自己的名义，而不是以用户的名义，向"服务提供商"进行认证。严格地说，客户端模式并不属于OAuth框架所要解决的问题。在这种模式中，用户直接向客户端注册，客户端以自己的名义要求"服务提供商"提供服务，其实不存在授权问题。

（A）客户端向认证服务器进行身份认证，并要求一个访问令牌。
（B）认证服务器确认无误后，向客户端提供访问令牌。

### 更新令牌

如果用户访问的时候，客户端的"访问令牌"已经过期，则需要使用"更新令牌"申请一个新的访问令牌。

## 实现

### Python

1. [Flask--搭建OAuth2.0认证服务器（一）](https://blog.csdn.net/qq_28877125/article/details/81318451)
2. [Flask--搭建OAuth2.0认证服务器（二）](https://blog.csdn.net/qq_28877125/article/details/81346427)
3. [使用Python实现OAuth2.0认证服务器](https://blog.csdn.net/liuchunming033/article/details/45564791)

### Java

1. 大多数情况下我们都需要实现自己的authentication server，好在spring 框架提供了一个基于spring security 的oauth框架来帮助实现对应的authentication server, resource server 以及client。

### javascript

1. 参考[oauthjs/node-oauth2-server](https://github.com/oauthjs/node-oauth2-server)。

## 案例分析

参考[github的oauth登陆系列之注册appkey](https://newsn.net/say/oauth-login-github-apply.html)
参考[github的oauth登陆的基本流程，oauth2.0原理解析](https://newsn.net/say/github-oauth-flow.html)

