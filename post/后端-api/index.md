# 后端-构建API服务


## 标准

1. [OpenAPI规范](https://www.openapis.org/)
2. [JSON Schema](http://json-schema.org/)

## 设计原则

```markdown
1. API版本放进URL中。
2. API执行前都需要进行认证和权限的判定（中间件）。`权限的判定，通过token获取userId，根据userId去判断请求的数据有无权限`
3. 对外网API中数据全为UUID不能使用数据库ID。
```

## 安全

### HTTPS &hearts;&hearts;

1. 对于敏感的接口，需要使用https协议。
2. [申请Let's Encrypt永久免费SSL证书](https://www.jianshu.com/p/3ae2f024c291)。

### Token（APP_KEY） &hearts;&hearts;&hearts;

1. 在用户成功登录时，系统可以返回客户端一个Token，并以Token-userID以键值对的形式存放进缓存服务器；
2. 后续客户端调用服务端的接口，都需要带上Token，而服务端需要校验客户端Token的合法性。Token不一致的情况下，服务端需要拦截该请求。

* [参考心知天气第一种安全验证方式](https://docs.seniverse.com/api/start/key.html)会在请求地址中泄露私钥。
* APP_ID（公钥）。

### 签名机制 &hearts;&hearts;&hearts;

1. 使用不对称加密（MD5或SHA-1）`对请求参数进行加密`，`生成签名`。`保证请求数据不会被篡改`。
2. 服务器将签名保存到缓存服务器，并设置超时，在超时时间内收到同一签名的请求，忽略。

示例：
```
1. 对除签名外的所有请求参数按key做的升序排列,value无需编码。
2. 把参数名和参数值连接成字符串，得到拼装字符串。
3. 用申请到的appkey连接到接拼装字符串头部和尾部，然后进行32位MD5加密，最后将到得MD5加密摘要转化成大写。
```

* [参考心知天气第二种安全验证方式](https://docs.seniverse.com/api/start/validation.html)不会在请求地址泄露私钥。
* [参考腾讯的API规范文档](https://mta.qq.com/docs/behavior_prediction_api.html#%E5%85%AC%E5%85%B1%E5%8F%82%E6%95%B0%E4%B8%8EAPI)。
* [参考阿里云的API规范](https://help.aliyun.com/document_detail/67820.html?spm=5176.11065259.1996646101.searchclickresult.3bed70ce3MIWjo)。

### 过载保护

对接口访问频率设置一定的阈值。

### 时间戳超时机制 &hearts;&hearts;&hearts;

用户每次请求都需要带上当前时间的时间戳，服务端比对时间戳与当前时间，如果时间差大于一定时间（比如5分钟），认为请求失效。`是防御DOS攻击的有效手段`。

### 异常封装

构建异常统一的处理框架，将服务可能出现的异常做统一封装，返回固定的code与msg，防止程序堆栈信息暴露。

### 请求和响应数据的加解密（可以使用https） &hearts;&hearts;&hearts;

1. 并非所有的请求和响应都需要加解密；
2. 加密算法有对称加密和非对称加密，AES是对称加密，RSA是非对称加密。AES加密数据是因为效率高，RSA运行速度慢,可以用于签名操作。`可以用RSA来加密传输AES的秘钥，用AES来加密数据，两者相互结合，优势互补`。
3. HTTPS比HTTP慢的原因都是因为需要让客户端与服务器端安全地协商出一个对称加密算法。剩下的就是通信时双方使用这个对称加密算法进行加密解密。

```markdown
1. 客户端启动，发送请求到服务端，服务端用RSA算法生成一对公钥和私钥，我们简称为pubkey1,prikey1，将公钥pubkey1返回给客户端。
2. 客户端拿到服务端返回的公钥pubkey1后，自己用RSA算法生成一对公钥和私钥，我们简称为pubkey2,prikey2，并将公钥pubkey2通过公钥pubkey1加密，加密之后传输给服务端。
3. 此时服务端收到客户端传输的密文，用私钥prikey1进行解密，因为数据是用公钥pubkey1加密的，通过解密就可以得到客户端生成的公钥pubkey2
4. 然后自己在生成对称加密，也就是我们的AES,其实也就是相对于我们配置中的那个16的长度的加密key,生成了这个key之后我们就用公钥pubkey2进行加密，返回给客户端，因为只有客户端有pubkey2对应的私钥prikey2，只有客户端才能解密，客户端得到数据之后，用prikey2进行解密操作，得到AES的加密key,最后就用加密key进行数据传输的加密，至此整个流程结束。
```

## GraphQL

### 优势

1. GraphQL 最大的优势是查询图状数据。
2. 前提是数据已经以图的数据结构进行保存。
3. 可以理解为基于RESTful的一种封装，一种新的api标准。
4. 所见即所得，相对RESTful API依赖于后端隐式的被动的数据约定，GraphQL更加显式，在获取数据和更新数据时更加主动。
5. 减少网络请求的使用，GraphQL可以实现对多个数据源的调用，合并成一份完整的数据给前端使用。
6. 参数类型强校验，GraphQL提供了强类型的schema机制，从而确保了参数类型的合法性。
————————————————
版权声明：本文为CSDN博主「小生叫不才」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/onsenOnly/java/article/details/102639327

### 原则

### 服务端和客户端必须同时支持

## 框架

1. [FastAPI](https://fastapi.tiangolo.com/#requirements)

