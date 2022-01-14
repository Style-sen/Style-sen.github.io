# 模块化开发及常用模块


# 理论

参考[详解JavaScript模块化开发](https://segmentfault.com/a/1190000000733959#articleHeader5)
参考[JavaScript中的模块化开发](https://www.jianshu.com/p/3832c00a44a7)

一个模块就是实现特定功能的文件，有了模块，我们就可以更方便地使用别人的代码，想要什么功能，就加载什么模块。模块开发需要遵循一定的规范，否则就都乱套了。

AMD规范的实现主要有RequireJS，CMD规范的主要实现有SeaJS。RequireJS在国外用的比较多，SeaJS在国内用的比较多，并且SeaJS的创始人为阿里的玉伯，所以SeaJS在阿里系用的非常广泛，包括京东等大厂也在用SeaJS，我们详细介绍的也是SeaJS。但是SeaJS已经停止维护了，因为在ES6中已经有了模块化的实现，随着ES6的普及，第三方的模块化实现将会慢慢的淘汰（但是这个在国内可能还要很多年）。


## Commonjs

1. 服务器端规范,`node.js用的就是CommonJS规范`.
2. 根据CommonJS规范，`一个单独的文件就是一个模块`。**每一个模块都是一个单独的作用域，也就是说，在该模块内部定义的变量，无法被其他模块读取，除非定义为global对象的属性**。
3. 在node.js中直接使用`require`引包，直接使用`exports`和`module.exports`暴露公开成员，并且npm基于CommonJs实现了自动加载和安装依赖。
```javascript
var i = 1;
var max = 30;
// module指的就是当前文件
module.exports = function () {
  for (i -= 1; i++ < max; ) {
    console.log(i);
  }
  max *= 1.1;
};
module.exports = {
  [插件名]:{

  }
}
//与下方一致
exports.[插件名] = {
  
}
```
### Promises/A
1. Promises/A是由CommonJS组织制定的异步模式编程规范，有不少库已根据该规范及后来经改进的`Promises/A+`规范提供了实现.
2. 参考[JS魔法堂：剖析源码理解Promises/A规范](https://imququ.com/post/promises-when-js.html)
#### 规范
1. 有限状态机
Promise（中文：承诺）其实为一个有限状态机，共有三种状态：pending（执行中）、fulfilled（执行成功）和rejected（执行失败）。
其中pending为初始状态，fulfilled和rejected为结束状态（结束状态表示promise的生命周期已结束）。
状态转换关系为：pending->fulfilled，pending->rejected。
随着状态的转换将触发各种事件（如执行成功事件、执行失败事件等）。  
2. 构造函数
Promise({Function} factory/*({Function} resolve, {Function} reject)*/) ，构造函数存在一个Function类型的入参factory，作为唯一一个修改promise对象状态的地方，其中factory函数的入参resolve的作用是将promise对象的状态从pending转换为fulfilled，而reject的作用是将promise对象的状态从pending转换为rejected。
入参 void resolve({Any} val)  ，当val为非thenable对象和promise对象时则会将val作为执行成功事件处理函数的入参，若val为thenable对象时则会执行thenable.then方法，若val为Promise对象时则会将该Promise对象添加到Promise对象单向链表中。
入参 void reject({Any} reason) ，reason不管是哪种内容均直接作为执行失败事件处理函数的入参。
注意：关于抛异常的做法，同步模式为 throw new Error("I'm synchronous way!") ，而Promise规范的做法是 reject(new Error("I'm asynchronous way!")); 
3. 实例方法
Promise then([{Function} onFulfilled[, {Function} onRejected]]) ，用于订阅Promise对象状态转换事件，入参onFulfilled为执行成功的事件处理函数，入参onRejected为执行失败的事件处理函数。两者的返回值均作为Promise对象单向链表中下一个Promise对象的状态转换事件处理函数的入参。而then方法的返回值是一个新的Promise对象并且已添加到Promise对象单向链表的末尾。
Promise catch({Function} onRejected) ，相当于 then(null, onRejected) 。
4. 类方法
Promise Promise.resolve({Any} obj) ，用于将非Promise类型的入参封装为Promise对象，若obj为非thenable对象则返回状态为fulfilled的Promise对象，对于非若入参为Promise对象则直接返回。
Promise Promise.reject({Any} obj) ，用于将非Promise类型的入参封装为状态为rejected的Promise对象。
Promise Promise.all({Array} array) ，当array中所有Promise实例的状态均为fulfilled时，该方法返回的Promise对象的状态也转为fulfilled（执行成功事件处理函数的入参为array数组中所有Promise实例执行成功事件处理函数的返回值），否则转换为rejected。
Promise Promise.race({Array} array) ，当array中有一个Promise实例的状态出现fulfilled或rejected时，该方法返回的Promise对象的状态也转为fulfilled或rejected。
5. thenable对象
拥有 then方法 的对象均称为thenable对象，并且thenable对象将作为Promise对象被处理。


## AMD(Asynchronous Module Definition)
1. 在浏览器端模块化开发的规范
2. 模块将被异步加载，模块加载不影响后面语句的运行。所有依赖某些模块的语句均放置在回调函数中。
3. AMD规范的实现主要有RequireJS

## CMD
1. CMD规范的主要实现有SeaJS

# 常用模块

## Browser和Nodejs共同支持

### AXIOS（HTTP客户端）

1. 基于promise用于浏览器和node.js的http客户端；
#### 特点

1. 支持浏览器和node.js
2. 支持promise
3. 能拦截请求和响应
4. 能转换请求和响应数据
5. 能取消请求
6. 自动转换JSON数据
7. 浏览器端支持防止CSRF(跨站请求伪造)

#### 拦截器

1. 你可以在then和catch之前拦截请求和响应。

```javascript
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 添加一个响应拦截器
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```

2. 如果之后想移除拦截器你可以这么做

```
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

3. 你也可以为axios实例添加一个拦截器

```
var instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```
### [bezier-js](https://www.npmjs.com/package/bezier-js)
1. 一个Node.js和客户端库，用于（二次和三次）贝塞尔曲线工作。
2. 用于执行Bezier曲线操作
3. 参考[贝塞尔曲线应用（贝塞尔插值）](https://www.cnblogs.com/hnfxs/p/3148743.html)使用两个点作为顶点，利用相邻的点生成2个控制点。

### [caolan/async](https://github.com/caolan/async)  Async utilities for node and the browser   *****
1. async模块是为了解决嵌套金字塔,和异步流程控制而生.

### [form-data](https://github.com/form-data/form-data)
1. A module to create readable `"multipart/form-data"` streams. Can be used to submit forms and file uploads to other web applications.

### [js-spline](https://www.npmjs.com/package/js-spline)
1. 程序包提供了JavaScript的算法实现，这些算法可从一组控制方式点生成各种类型的样条。
2. 插值之后的曲线经过样本点，拟合的曲线不一定经过样本点。
3. [插值算法比较](http://blog.sina.com.cn/s/blog_6316e2af0101l548.html):1. 反距离加权法（Inverse Distance Weighted）两个物体相似性随他们间的距离增大而减少。2. 样条插值法（Spline）样条插值是使用一种数学函数，对一些限定的点值，通过控制估计方差，利用一些特征节点，用多项式拟合的方法来产生平滑的插值曲线。这种方法适用于逐渐变化的曲面，如温度、高程、地下水位高度或污染浓度等。3、克里金法（Kriging）这种方法认为在空间连续变化的属性是非常不规则的，用简单的平滑函数进行模拟将出现误差，用随机表面函数给予描述会比较恰当。4、离散平滑插值（Discrete Smooth Interpolation）欲在一个离散化数据点间建立相互联络的网络，如果网络上的已知节点值满足某种约束条件，则未知节点上的值可以通过解线性方程而得到。5、趋势面光滑插值（Trend Surface）作为一个非精确的插值方法，趋势面插值用多项式表示的线或面按最小二乘法原理对数据点进行拟合.6.贝塞尔插值。

### [kennethjiang/js-file-download](https://github.com/kennethjiang/js-file-download)保存数据到文件中

### [Lodash](https://www.lodashjs.com/)是一个一致性、模块化、高性能的 JavaScript 实用工具库 *****

### [smartprocure/futil-js](https://github.com/smartprocure/futil-js) Lodash的补充  *****

### [moment](https://momentjs.com/docs/) 格式化分析日期和时间   *****

### [moment/luxon](https://github.com/moment/luxon) A library for working with dates and times in JS  *****

1. `每年的最后几天是下一年的第一周`。

#### DateTime`const { DateTime } = require("luxon")`

```js
var dt = DateTime.local(2017, 5, 15, 8, 30);
var now = DateTime.local();
dt = DateTime.fromObject({day: 22, hour: 12, zone: 'America/Los_Angeles', numberingSystem: 'beng'})
DateTime.fromISO("2017-05-15")          //=> May 15, 2017 at midnight
DateTime.fromISO("2017-05-15T08:30:00") //=> May 15, 2017 at 8:30
DateTime.local().toString() //=> '2017-09-14T03:20:34.091-04:00'
dt = DateTime.local()
dt.year     //=> 2017
dt.month    //=> 9
dt.day      //=> 14
dt.second   //=> 47
dt.weekday  //=> 4
dt.zoneName     //=> 'America/New_York'
dt.offset       //=> -240
dt.daysInMonth  //=> 30
dt.toLocaleString()      //=> '9/14/2017'
dt.toLocaleString(DateTime.DATETIME_MED) //=> 'September 14, 3:21 AM'
dt.toISO() //=> '2017-09-14T03:21:47.070-04:00'
var dt = DateTime.local();
dt.plus({hours: 3, minutes: 2});　　　　//　计算
dt.minus({days: 7});        // 减
dt.startOf('day');
dt.endOf('hour');
var dt = DateTime.local();
dt.set({hour: 3}).hour   //设置时间
var dt = DateTime.local();
var f = {month: 'long', day: 'numeric'};
dt.setLocale('fr').toLocaleString(f)      //=> '14 septembre'
dt.setLocale('en-GB').toLocaleString(f)   //=> '14 September'
dt.setLocale('en-US').toLocaleString(f)  //=> 'September 14'
Info.months('long', {locale: 'fr'}) //=> [ 'janvier', 'février', 'mars', 'avril', ... ]
DateTime.fromObject({zone: 'America/Los_Angeles'}) // now, but expressed in LA's local time
DateTime.local().setZone('America/Los_Angeles') // same
DateTime.utc(2017, 5, 15);
DateTime.utc();
DateTime.local().toUTC();
DateTime.utc().toLocal();
var dur = Duration.fromObject({hours: 2, minutes: 7});
dt.plus(dur);
dur.hours   //=> 2
dur.minutes //=> 7
dur.seconds //=> 0
dur.as('seconds') //=> 7620
dur.toObject()    //=> { hours: 2, minutes: 7 }
dur.toISO()       //=> 'PT2H7M'
now = DateTime.local();
later = DateTime.local(2020, 10, 12);
i = Interval.fromDateTimes(now, later);
i.length()                             //=> 97098768468
i.length('years')                //=> 3.0762420239726027
i.contains(DateTime.local(2019))       //=> true
i.toISO()       //=> '2017-09-14T04:07:11.532-04:00/2020-10-12T00:00:00.000-04:00'
i.toString()    //=> '[2017-09-14T04:07:11.532-04:00 – 2020-10-12T00:00:00.000-04:00)
```

### [moment-timezone](https://momentjs.com/timezone/)  分析和格式化日期时间在各个时区   *****

### [Ramda 一款实用的 JavaScript 函数式编程库](https://ramda.cn/)

### [range]()  类似python的range

### [Simplify.js](https://mourner.github.io/simplify-js/)  压缩点数（轨迹不失真）

### [Underscore.js](https://underscorejs.bootcss.com/)  一个 JavaScript 工具库    *****
1. 它提供了一整套函数式编程的实用功能，但是没有扩展任何 JavaScript 内置对象.

### [when.js](https://github.com/cujojs/when) promise + async   ******
1. A solid, fast Promises/A+ and when() implementation, plus other async goodies.
2. 参考[异步编程：When.js快速上手](https://imququ.com/post/promises-when-js.html).
3. 优化promise.

## NodeJs支持

### [Automattic/node-canvas](https://github.com/Automattic/node-canvas) 在node中实现canvas渲染

### [BULL](https://github.com/OptimalBits/bull)
1. KUE不再维护。

#### 简单队列
1.  简单创建一个队列实例。
```
const myFirstQueue = new Bull('my-first-queue');
```
2. 一个简单队列大致有3个主要角色： A job producer, a job consumer or/and an events listener.
3. 虽然一个给定的实例可以用于3个角色，但通常将生产者和消费者分成几个实例。 一个给定的队列，总是由其实例化名称（上例中的my-first-queue）引用，可以有许多生产者，许多消费者和许多听众。 一个重要的方面是，即使当时没有可用的消费者，生产者也可以将作业添加到队列中：队列提供异步通信，这是使它们如此强大的功能之一。
4. 相反，您可以让一个或多个worker从队列中消费作业，它将按照给定的顺序消费这些作业:FIFO(缺省值)、LIFO或根据优先级。

#### 生产者
```
const myFirstQueue = new Bull('my-first-queue');
const job = await myFirstQueue.add({
  foo: 'bar'
});
```

#### 消费者
```
const myFirstQueue = new Bull('my-first-queue');
myFirstQueue.process(async (job, data) => {
  return doSomething(data);
});
```
1. 有时您需要向外部侦听器提供作业的进度信息，这可以通过在作业对象上使用progress方法轻松完成：
```
myFirstQueue.process( async (job, data) => {
  let progress = 0;
  for(i = 0; i < 100; i++){
    await doSomething(data);
    progress += 10;
    job.progress(progress);
  }
});
```

#### 选项
##### 速率限制
```
// Limit queue to max 1.000 jobs per 5 seconds.
const myRateLimitedQueue = new Queue('rateLimited', {
  limiter: {
    max: 1000,
    duration: 5000
  }
});
```
##### Named jobs
```
// Jobs producer
const myJob = await transcoderQueue.add('image', { input: 'myimagefile' });
const myJob = await transcoderQueue.add('audio', { input: 'myaudiofile' });
const myJob = await transcoderQueue.add('video', { input: 'myvideofile' });
// Worker
transcoderQueue.process('image', processImage);
transcoderQueue.process('audio', processAudio);
transcoderQueue.process('video', processVideo);
```
1. 每一个命名的JOB必须有一个对应的processer。
##### Sandboxed Processors

#### JOB类型
##### LIFO
后进先出
```
const myJob = await myqueue.add({ foo: 'bar' }, { lifo: true });
```
##### Delayed
1. 延时。
2. 设置的为最小延时，过了这个时间，才放进可执行队列中。
```
// Delayed 5 seconds
const myJob = await myqueue.add({ foo: 'bar' }, { delay: 5000 });
```
##### 优先级
##### 可重复

### [generic-pool](https://github.com/coopernurse/node-pool) 连接池     *****
1. NNODEJS的一般链接池模块。

### [ioredis](https://github.com/luin/ioredis#basic-usage) *****
一个健壮的、以性能为中心的、功能全面的Redis Node.js客户端.
```js
redis.set("foo", "bar");
redis.set("dir:key",value);  //可以创建dir文件夹，归类key
redis.get("foo", function(err, result) {    // get value
  console.log(result);
});
redis.del("foo");  // 删除键

// Or using a promise if the last argument isn't a function
redis.get("foo").then(function(result) {
  console.log(result);
});

redis.keys('course*'); // 列出匹配到给定字符串的key的列表

// Arguments to commands are flattened, so the following are the same:
redis.sadd("set", 1, 3, 5, 7);     //集合
redis.sadd("set", [1, 3, 5, 7]);

// All arguments are passed directly to the redis server:
redis.set("key", 100, "EX", 10);
redis.setex("key", time, "msg");//存进redis中，并设置超时时间。
```

### [is-type-of](https://github.com/node-modules/is-type-of)

1. `complete type checking for node`类型检测。

### [JsonWebToken](https://github.com/auth0/node-jsonwebtoken) JWT
#### jwt.sign(payload, secretOrPrivateKey, [options, callback]) 生成Token
1. `secretOrPrivateKey` is a string, buffer, or object 包含secret(for HMAC algorithms)或者the PEM encoded private key(for RSA and ECDSA). In case of a private key with passphrase an object { key, passphrase } can be used (based on crypto documentation), in this case be sure you pass the algorithm option.
2. [OpenSSL生成pem，der文件](https://www.jianshu.com/p/7575f7976dad)
#### jwt.verify(token, secretOrPublicKey, [options, callback])  校验Token

### [StratoDem/pandas-js](https://stratodem.github.io/pandas.js-docs/#introduction)
#### DataFrame
支持常用的统计。

### [puppeteer](https://zhaoqize.github.io/puppeteer-api-zh_CN/#/) 

### [request](https://github.com/request/request)    *****

1. 业务不复杂的情况下，使用Nodejs的原生http模块的客户端功能可满足需求，但在业务较复杂时使用http模块工作量会比较大，使用第三方request模块会让http请求变的简单。
2. Request模块封装了http请求，使你可以用很简单的方式处理http请求，同时也支持HTTPS和请求重定向.

### [Request-Promise](https://github.com/request/request-promise)    ******
1. request增加promise的支持。
#### 流操作
#### form相关

## Browser支持
