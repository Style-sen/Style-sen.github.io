# 后端-web框架-node-egg


Egg 奉行『约定优于配置』

## 渐进式开发

```markdown
1. 最初始的状态：直接在框架中写业务代码。
2. 插件的雏形：发现业务代码具有通用性，可以写成插件。
3. 抽成独立插件： 插件稳定之后，做成node module。
4. 沉淀到框架：重复上述的过程，很快我们会积累了好几个插件和配置，并且我们会发现，在团队的大部分项目中，都会用到这些插件，考虑抽象出一个适合团队业务场景的框架。
5. 注意：不管是应用/插件/框架，都必须编写单元测试，并尽量实现 100% 覆盖率。
```

## 框架内置基础对象

### Application

1. Application 是`全局应用对象，在一个应用中，只会实例化一个`，它继承自 Koa.Application。

#### 事件

```javascript
// app.js

module.exports = app => {
  app.once('server', server => {    //该事件一个 worker 进程只会触发一次，在 HTTP 服务完成启动后，会将 HTTP server 通过这个事件暴露出来给开发者
    // websocket
  });
  app.on('error', (err, ctx) => {    //运行时有任何的异常被 onerror 插件捕获后，都会触发 error 事件，将错误对象和关联的上下文（如果有）暴露给开发者，可以进行自定义的日志记录上报等处理
    // report error
  });
  app.on('request', ctx => {    //应用收到请求和响应请求时，分别会触发 request 和 response 事件，并将当前请求上下文暴露出来，开发者可以监听这两个事件来进行日志记录
    // log receive request
  });
  app.on('response', ctx => {
    // ctx.starttime is set by framework
    const used = Date.now() - ctx.starttime;
    // log total cost
  });
};
```

#### 获取方式

1. Application 对象几乎可以在编写应用时的任何一个地方获取到.
2. 和 Koa 一样，在 Context 对象上，可以通过 ctx.app 访问到 Application 对象。

```js
// 使用egg-socket之后，关闭连接
app.io.of('/').adapter.remoteDisconnect(id, true, err => {
    logger.error(err);
  });
```

### Context

Context 是一个请求级别的对象，继承自 Koa.Context。在`每一次收到用户请求时，框架会实例化一个 Context 对象`，这个对象封装了这次用户请求的信息，并提供了许多便捷的方法来获取请求参数或者设置响应信息。`框架会将所有的 Service 挂载到 Context 实例上`，`一些插件也会将一些其他的方法和对象挂载到它上面（egg-sequelize 会将所有的 model 挂载在 Context 上）`。

#### Ctx获取方式

1. 最常见的 Context 实例获取方式是在 Middleware, Controller 以及 Service 中.

```js
ctx 作为函数参数
    .query    //请求url中的参数(=)
    .request
        .body    //获取post请求的body.请求内容类型必须为为`application/json`
        .header
            .host    // 域名
            .connection    // upgrade
            .content-length
            .x-forwarded-for    // IP地址
            .user-agent    // 用户客户端
            .accept    //
            .cache-control
            .postman-token
            .accept-encoding    //gzip,deflate,br
        .method    // 请求方法 GET
        .url    // 路由
        .query  //参数
    .res
    .response
        .status    // 404(初始)
        .msg    //Not Found(初始)
        .header
    .socket    //使用egg-socket插件之后
        .emit('res', 'connected!');    //
        .id //socket连接ID
    .app    //项目有关
        .env
        .name
        .baseDir
        .subdomainOffset
        .config
        .controller
        .httpclient
        .loggers
        .middlewares
        .router
        .serviceClasses
    .params    // URL中的参数，定义router时会定义
    .originalUrl
    .req
    .res
```

### Request & Response

Request 是一个请求级别的对象，继承自 Koa.Request。封装了 Node.js 原生的 HTTP Request 对象，提供了一系列辅助方法获取 HTTP 请求常用参数。
Response 是一个请求级别的对象，继承自 Koa.Response。封装了 Node.js 原生的 HTTP Response 对象，提供了一系列辅助方法设置 HTTP 响应。

### Controller

1. `框架提供了一个 Controller 基类，并推荐所有的 Controller 都继承于该基类实现`。这个 Controller 基类有下列属性：

```js
ctx - 当前请求的 Context 实例。
app - 应用的 Application 实例。
config - 应用的配置。
service - 应用所有的 service。
logger - 为当前 controller 封装的 logger 对象。
```

### Service

1. `框架提供了一个 Service 基类，并推荐所有的 Service 都继承于该基类实现`。
2. Service 基类的属性和 Controller 基类属性一致
3. `每一次用户请求，框架都会实例化对应的 Service 实例`。
4. Service 不是单例，是 请求级别 的对象，框架在每次请求中首次访问 ctx.service.xx 时`延迟实例化`，所以 Service 中可以通过 this.ctx 获取到当前请求的上下文。

#### 作用

1. 保持 Controller 中的逻辑更加简洁。
2. 保持业务逻辑的独立性，抽象出来的 Service 可以被多个 Controller 重复调用。
3. 将逻辑和展现分离，更容易编写测试用例。

### Helper

1. Helper 用来提供一些实用的 utility 函数。它的作用在于我们可以将一些常用的动作抽离在 helper.js 里面成为一个独立的函数，这样可以用 JavaScript 来写复杂的逻辑，避免逻辑分散各处，同时可以更好的编写测试用例。
2. Helper 自身是一个类，有和 Controller 基类一样的属性，它`也会在每次请求时进行实例化`，因此 `Helper 上的所有函数也能获取到当前请求相关的上下文信息`。

### Config

7.1. 获取方式

### Logger

8.1. App Logger
8.2. App CoreLogger
8.3. Context Logger
8.4. Context CoreLogger
8.5. Controller Logger & Service Logger

### Subscription

## [目录结构](https://eggjs.org/zh-cn/basics/structure.html)

1. 文件名不能用下划线，有可能识别不了。

```javascript
egg-project
├── package.json
├── app.js (可选)    //自定义启动时的初始化工作，可选
├── agent.js (可选)
├── app
  |   ├── router.js    //配置 URL 路由规则
│  ├── controller    //解析用户的输入，处理后返回相应的结果,注意，支持多级目录，默认文件名为驼峰式
│    |   └── home.js
│  ├── service (可选)    //编写业务逻辑层，可选，建议使用，*注意，支持多级目录，默认文件名为驼峰式，可以修改LoaderAPI改变文件名*
│    |   └── user.js
│  ├── middleware (可选)    //编写中间件，可选（例如同意错误处理）
│    |   └── response_time.js
│  ├── schedule (可选)    //定时任务，可选
│    |   └── my_task.js
│  ├── public (可选)    //放置静态资源，可选
│    |   └── reset.css
│  ├── core (可选)    //基于内部类可以定义中间类（定义中间类使用constructor(ctx)必须使用ctx作为入口参数）
│  ├── utils (可选)    //自定义方法
│  ├── lib (可选)    //自定义模块
│  ├── view (可选)    //放置模板文件，可选，由模板插件约定，具体参见模板渲染
│    |   └── home.tpl
  |   ├── model    //放置领域模型，可选，由领域类相关插件约定，如 egg-sequelize.   不能存在子目录，而且模型名必须要和模型文件名字符相同（可以不区分大小写）
│  └── extend (可选)    //框架的扩展，可选
│       ├── helper.js (可选)
│       ├── request.js (可选)
│       ├── response.js (可选)
│       ├── context.js (可选)
│       ├── application.js (可选)
│       └── agent.js (可选)
├── config    //编写配置文件
  |   ├── plugin.js    //配置需要加载的插件
  |   ├── config.default.js
│  ├── config.prod.js
  |   ├── config.test.js (可选)
  |   ├── config.local.js (可选)
  |   └── config.unittest.js (可选)
└── test    //单元测试
    ├── middleware
      |   └── response_time.test.js
    └── controller
        └── home.test.js
```

### Service\Controller使用core定义的类  ******

1. core中的类基于Service或者Controller创建的。`如果有了自己的属性，需要用new；如果没有自己的属性，可以使用extends`
2. core中的类不是基于Service或者Controller创建的。`只能使用new`

### controller

```js
class UserController extends Controller {
  async info() {
    const { ctx } = this;
    ctx.body = {
      name: `hello ${ctx.params.id}`,  // params获取url中的参数。
    };
  }
}
```

### router

1. Router 主要用来描述请求 URL 和具体承担执行动作的 Controller 的对应关系， 框架约定了 app/router.js 文件用于统一所有路由规则。

```js
// app/router.js
module.exports = app => {
  const { router, controller } = app;
  router.get('/user/:id', controller.user.info);    // 定义/user/1
  router.get('/home', controller.home);
  router.get('/user/:id', controller.user.page);
  router.post('/admin', isAdmin, controller.admin);
  router.post('/user', isLoginUser, hasAdminPermission, controller.user.create);
  router.post('/api/v1/comments', controller.v1.comments.create); // app/controller/v1/comments.js
  router.resources('topics', '/api/v2/topics', app.controller.topics);
};
```

#### 路由完整定义主要包括5个主要部分:

`router.verb('router-name', 'path-match', middleware1, ..., middlewareN, app.controller.action);`

1. verb - 用户触发动作，支持 get，post 等所有 HTTP 方法

```js
router.head - HEAD
router.options - OPTIONS
router.get - GET
router.put - PUT
router.post - POST
router.patch - PATCH
router.delete - DELETE
router.del - 由于 delete 是一个保留字，所以提供了一个 delete 方法的别名。
router.redirect - 可以对 URL 进行重定向处理，比如我们最经常使用的可以把用户访问的根目录路由到某个主页。
```

2. router-name 给路由设定一个别名，可以通过 Helper 提供的辅助函数 pathFor 和 urlFor 来生成 URL。(可选)
3. path-match - 路由 URL 路径。
4. middleware1 - 在 Router 里面可以配置多个 Middleware。(可选)
5. controller - 指定路由映射到的具体的 controller 上，controller 可以有两种写法：

```js
app.controller.user.fetch - 直接指定一个具体的 controller
'user.fetch' - 可以简写为字符串形式
```

#### RESTful 风格的 URL 定义

通过 app.resources 方法，我们将 posts 这个资源的增删改查接口映射到了 app/controller/posts.js 文件。

只需要在 posts.js 里面实现对应的函数就可以了。

Method|	Path|	RouteName|	Controller.Action
--|--|--|--
GET	|/posts	|posts	|app.controllers.posts.index
GET	|/posts/new	|new_post	|app.controllers.posts.new
GET	|/posts/:id	|post|	app.controllers.posts.show
GET	|/posts/:id/edit	|edit_post|	app.controllers.posts.edit
POST|	/posts	|posts	|app.controllers.posts.create
PUT	|/posts/:id	|post|	app.controllers.posts.update
DELETE|	/posts/:id	|post|	app.controllers.posts.destroy

### 单元测试

#### 约定

1. 我们约定 test 目录为存放所有测试脚本的目录，测试所使用到的 fixtures 和相关辅助脚本都应该放在此目录下。
2. 测试脚本文件统一按 ${filename}.test.js 命名，必须以 .test.js 作为文件后缀。

一个应用的测试目录示例：

```
test
├── controller
│   └── home.test.js
├── hello.test.js
└── service
    └── user.test.js
```

### 配置

1. 框架提供了强大且可扩展的配置功能，可以自动合并应用、插件、框架的配置，按顺序覆盖，且可以根据环境维护不同的配置。合并后的配置可直接从 app.config 获取。

## 启动自定义

1. 我们常常需要在应用启动期间进行一些初始化工作，等初始化完成后应用才可以启动成功，并开始对外提供服务。
2. 框架提供了统一的入口文件（app.js）进行启动过程自定义，这个文件返回一个 Boot 类，我们可以通过定义 Boot 类中的生命周期方法来执行启动应用过程中的初始化工作。
3. 框架提供了这些 生命周期函数供开发人员处理：

* 配置文件即将加载，这是最后动态修改配置的时机（configWillLoad）
* 配置文件加载完成（configDidLoad）
* 文件加载完成（didLoad）
* 插件启动完毕（willReady）
* worker 准备就绪（didReady）
* 应用启动完成（serverDidReady）
* 应用即将关闭（beforeClose）

4. 我们可以在 app.js 中定义这个 Boot 类，下面我们抽取几个在应用开发中常用的生命周期函数来举例：

```javascript
// app.js
class AppBootHook {
  constructor(app) {
    this.app = app;
  }

  configWillLoad() {
    // 此时 config 文件已经被读取并合并，但是还并未生效
    // 这是应用层修改配置的最后时机
    // 注意：此函数只支持同步调用

    // 例如：参数中的密码是加密的，在此处进行解密
    this.app.config.mysql.password = decrypt(this.app.config.mysql.password);
    // 例如：插入一个中间件到框架的 coreMiddleware 之间
    const statusIdx = this.app.config.coreMiddleware.indexOf('status');
    this.app.config.coreMiddleware.splice(statusIdx + 1, 0, 'limit');
  }

  async didLoad() {
    // 所有的配置已经加载完毕
    // 可以用来加载应用自定义的文件，启动自定义的服务

    // 例如：创建自定义应用的示例
    this.app.queue = new Queue(this.app.config.queue);
    await this.app.queue.init();

    // 例如：加载自定义的目录
    this.app.loader.loadToContext(path.join(__dirname, 'app/tasks'), 'tasks', {
      fieldClass: 'tasksClasses',
    });
  }

  async willReady() {
    // 所有的插件都已启动完毕，但是应用整体还未 ready
    // 可以做一些数据初始化等操作，这些操作成功才会启动应用

    // 例如：从数据库加载数据到内存缓存
    this.app.cacheData = await this.app.model.query(QUERY_CACHE_SQL);
  }

  async didReady() {
    // 应用已经启动完毕

    const ctx = await this.app.createAnonymousContext();
    await ctx.service.Biz.request();
  }

  async serverDidReady() {
    // http / https server 已启动，开始接受外部请求
    // 此时可以从 app.server 拿到 server 的实例

    this.app.server.on('timeout', socket => {
      // handle socket timeout
    });
  }
}

module.exports = AppBootHook;
```

**注意：在自定义生命周期函数中不建议做太耗时的操作，框架会有启动的超时检测。**

## [中间件](https://eggjs.org/zh-cn/basics/middleware.html)

1. Egg 是基于 Koa 实现的，所以 Egg 的中间件形式和 Koa 的中间件形式是一样的，都是基于洋葱圈模型。每次我们编写一个中间件，就相当于在洋葱外面包了一层。

## 加载器

1. Egg 在 Koa 的基础上进行增强最重要的就是基于一定的约定，根据功能差异将代码放到不同的目录下管理，对整体团队的开发成本提升有着明显的效果。`Loader 实现了这套约定`，并抽象了很多底层 API 可以进一步扩展。
2. `Egg 是一个底层框架，应用可以直接使用，但 Egg 本身的插件比较少，应用需要自己配置插件增加各种特性`，比如 MySQL。
3. 当应用达到一定数量，我们会发现大部分应用的配置都是类似的，这时`可以基于 Egg 扩展出一个框架`，应用的配置就会简化很多。

### 应用和插件、框架之间的关系

1. 在应用中完成业务，需要指定一个框架才能运行起来，当需要`某个特性场景的功能时`可以配置插件（比如 MySQL）。
2. `插件只完成特定功能`，当两个独立的功能有互相依赖时，还是分开两个插件，但需要配置依赖。
3. 框架是一个启动器（默认就是 Egg），必须有它才能运行起来。`框架还是一个封装器，将插件的功能聚合起来统一提供，框架也可以配置插件`。
4. 在框架的基础上还可以扩展出新的框架，也就是说`框架是可以无限级继承的`，有点像类的继承。
5. Egg 将应用、框架和插件都称为`加载单元（loadUnit）`，因为`在代码结构上几乎没有什么差异`，目录结构类似有一些差异。

|文件|应用|框架|插件|
|--|--|--|--|
|package.json|✔︎|✔︎|✔︎
|config/plugin.{env}.js|✔︎|✔︎|
|config/config.{env}.js|✔︎|✔︎|✔︎
|app/extend/application.js|✔︎|✔︎|✔︎
|app/extend/request.js|✔︎|✔︎|✔︎
|app/extend/response.js|✔︎|✔︎|✔︎
|app/extend/context.js|✔︎|✔︎|✔︎
|app/extend/helper.js|✔︎|✔︎|✔︎
|agent.js|✔︎|✔︎|✔︎
|app.js|✔︎|✔︎|✔︎
|app/service|✔︎|✔︎|✔︎
|app/middleware|✔︎|✔︎|✔︎
|app/controller|✔︎||
|app/router.js|✔︎||

6. `文件按表格内的顺序自上而下加载`.
7. 加载优先级：插件->框架->应用。
8. 插件之间的顺序由依赖关系决定，`被依赖方先加载，无依赖按 object key 配置顺序加载`；
9. 框架按`继承顺序加载，越底层越先加载`。
10. `加载时如果遇到同名的会覆盖，可以重写`，比如想要覆盖 ctx.ip 可以直接在应用的 app/extend/context.js 定义 ip 就可以了。

### 加载器扩展

1. Loader 的扩展只能在框架进行。

## 插件

1. 需要一套更加强大的机制，来管理、编排那些`相对独立的业务逻辑`。

### 常用插件

1. 插件合集[eggjs/awesome-egg](https://github.com/eggjs/awesome-egg)
2. 插件合集[github搜索](https://github.com/topics/egg-plugin)

#### [egg-cors](https://github.com/eggjs/egg-cors)

1. [egg.js作为服务端接受post跨域请求](https://blog.csdn.net/Cappuccino200/article/details/82535201)

#### [eggjs/egg-prometheus](https://github.com/eggjs/egg-prometheus)
1. Prometheus plugin for egg framework.

#### [jianglin-wu/egg-exporter](https://github.com/jianglin-wu/egg-exporter)
1. 基于 egg-prometheus 开发，增加更多性能指标，为 egg 提供 Prometheus 功能支持。

#### [egg-redis]

基于ioredis。

#### [egg-Sequelize](https://github.com/eggjs/egg-sequelize)

1. **model定义文件在model目录下，不能存在子目录，而且模型名必须要和模型文件名字符相同（可以不区分大小写）**
2. **外键需要定义在model中**
3. `const Op = app.Sequelize.Op;`
4. 不需要多个关联关系并存，比如使用了`belongsTo`不用再使用`hasMany`。但是`belongsTo`只能用源model联查目标model，如果需要反过来的话，还需要hasOne或者hasMany。
5. `一个model定义中只能出现一个Model.associate=function(){},多条关联关系都写在其中`。

```javascript
'use strict';

module.exports = app => {
  const { STRING, INTEGER, DATE } = app.Sequelize;

  const User = app.model.define('user', {
    id: {
      type: INTEGER,
      primaryKey: true,
      autoIncrement: true,
    },
    name: STRING(30),
    age: INTEGER,
    created_at: DATE,
    updated_at: DATE,
  });

  User.associate = function() {    // 不能使用prototype
    app.model.User.hasMany(app.model.Post, { as: 'posts' });
  };

  return User;
};  

'use strict';

module.exports = app => {
  const { STRING, INTEGER, DATE } = app.Sequelize;

  const Post = app.model.define('post', {  // model名称
    id: {
      type: INTEGER,
      primaryKey: true,
      autoIncrement: true,
    },
    title: STRING(30),
    content: STRING(255),
    user_id: INTEGER,
    created_at: DATE,
    updated_at: DATE,
  },
  {
    // model 配置
  });

  //关联
  Post.associate = function() {
    app.model.Post.belongsTo(app.model.User, { as: 'user', foreignKey: 'user_id' });
  };
  //方法
  Post.findByIdWithUser = async function(id, userId) {
    return await this.findOne({
      where: { id, user_id: userId },
    });
  };

  return Post;
};
```

#### [eggjs/egg-router-plus](https://github.com/eggjs/egg-router-plus)

```
this plugin will auto load router define at app/router/**/*.js.
```

#### [egg-jwt](https://github.com/okoala/egg-jwt)

#### [brickyang/egg-bull](https://github.com/brickyang/egg-bull)

1. 增加任务队列。
2. 将process方法放进app.js中。

#### [egg-validate]()

1. 作用：对参数进行检验。
2. 对API接口参数进行校验。
3. See [parameter](https://github.com/node-modules/parameter) for more information such as custom rule.

|类别|可能的值|
|--|--|
|type|属性的类型|
|Required|若为false，此参数可以为null或undefined。默认为true|
|length||
|convertType|Make parameter convert the input param to the specific type, support int, number, string and boolean, also support a function to customize your own convert method.|
|default|The default value of property, once the property is allowed non-required and missed, parameter will use this as the default value. This may change the original input params.|
|widelyUndefined | override options.widelyUndefined|

##### type可能的值

|type的值|说明|额外的属性|
|--|--|--|
string: Must be of type string. This is the default type.
|number| Must be of type number||
|boolean|Must be of type boolean||
method: Must be of type function.
regexp: Must be an instance of RegExp or a string that does not generate an exception when creating a new RegExp.
|integer(int)| Must be of type number and an integer|max,min|
float: Must be of type number and a floating point number.
|array: Must be an array as determined by Array.isArray.||
object: Must be of type object and not Array.isArray.
enum: Value must exist in the enum.
date: Value must be valid as determined by Date
url: Must be of type url.
hex: Must be of type hex.
email: Must be of type email.
any: Can be any type.

#### [egg-socket](https://github.com/eggjs/egg-socket.io)

1. 框架提供了 egg-socket.io 插件，增加了以下开发规约：
* namespace: 通过配置的方式定义 namespace（命名空间）
* middleware: 对每一次 socket 连接的建立/断开、每一次消息/数据传递进行预处理
* controller: 响应 socket.io 的 event 事件
* router: 统一了 socket.io 的 event 与 框架路由的处理配置方式
2. `egg-socket.io 内置了 socket.io-redis，在 cluster 模式下，使用 redis 可以较为简单的实现 clients/rooms 等信息共享`。
3. 开启 redis 后，程序在启动时会尝试连接到 redis 服务器 此处 redis 仅用于存储连接实例信息，参见 #server.adapter。

```
chat
├── app
│   ├── extend
│   │   └── helper.js
│   ├── io
│   │   ├── controller
│   │   │   └── default.js    //对连接之后的数据交互调用此，对socket.id独立
│   │   └── middleware
│   │       ├── connection.js    //处理链接的时候调用，每次新建连接的时候处理（连接或断开）在配置中`connectionMiddleware`设置
│   │       └── packet.js    //针对消息的处理
│   └── router.js
├── config
└── package.json
```

对应的文件都在 app/io 目录下

##### router（加入router）

```
module.exports = app => {
  // app.io.of('/')
  app.io.route('chat', app.io.controller.chat.index);

  // app.io.of('/chat')
  app.io.of('/chat').route('chat', app.io.controller.chat.index);
};
```

##### Namespace (nsp)

1. namespace 通常意味分配到不同的接入点或者路径，如果客户端没有指定 nsp，则默认分配到 "/" 这个默认的命名空间。
2. 在 socket.io 中我们通过 of 来划分命名空间；鉴于 nsp 通常是预定义且相对固定的存在，框架将其进行了封装，采用配置的方式来划分不同的命名空间。

##### Room

1. room 存在于 nsp 中，通过 join/leave 方法来加入或者离开; 框架中使用方法相同；
2. 注意： `每一个 socket 连接都会拥有一个随机且不可预测的唯一 id Socket#id，并且会自动加入到以这个 id 命名的 room 中`

##### 踩过的坑

1. 在连接中间件中，使用`await next()`来分别处理连接和断开连接。需要注意的是，断开连接响应发生在断开连接超时的时候，所以可能断开连接响应发生在建立连接之后。
2. `一种方案`：此时需要判断old_id和current_id是否一致的情况；只需要连接时判断有无C_id,没有C_id更新C_id和O_id；有C_id,只更新C_id；超时回调判断C_id和O_id是否一致，一致删除C-id和O_id;不一致，更新O_id.
3. 上述方案有一个问题，在异步的时候，会出现超时回调距离时间很近，会导致更新缓存里的值出问题。
4. 采用`第二种方案`：将socket id也作为缓存key值，value值为时间，这要每次取value值最大的情况。
5. 在cluster模式下，socket只是连接到其中一个进程，所以需要使用[socketio/socket.io-redis](https://github.com/socketio/socket.io-redis)共享连接信息，但是限制有方法调用。
```
By running socket.io with the socket.io-redis adapter you can run multiple socket.io instances in different processes or servers that can all broadcast and emit events to and from each other.

So any of the following commands:

io.emit('hello', 'to all clients');
io.to('room42').emit('hello', "to all clients in 'room42' room");

io.on('connection', (socket) => {
  socket.broadcast.emit('hello', 'to all clients except sender');
  socket.to('room42').emit('hello', "to all clients in 'room42' room except sender");
});
will properly be broadcast to the clients through the Redis Pub/Sub mechanism.

If you need to emit events to socket.io instances from a non-socket.io process, you should use socket.io-emitter.
```
6. `app.io.sockets.connected[socketId]`和`const nsp = app.io.of('/');nsp.sockets[id]`两种方法在cluster之下不能发送数据,因为和进程有关，只能用上述方法。

## 框架开发

### [egg-init脚手架工具](https://yaozhanxin.github.io/2018/09/22/node/egg-init/)
1. 新版本增加了microservice 继承自`egg-cloud`框架。
2. 可以快速开始创建项目工程。

### 参考[egg-cloud](https://github.com/eggjs/egg-cloud)

### 使用框架

1. 配置package.json
```json
...
"egg":{
  "framework":"egg-cloud"
}
...
"dependencies":{
  "egg-cloud":"^0.2.0"    // 不用egg
}
...
```

## Restful API


## 生产部署

### 构建

1. 只安装 dependencies 的依赖.

```shell
$ npm install --production    
```

2. 打包

```
$ tar -zcvf ../release.tgz .
```

### 部署

1. 框架内置了 egg-cluster 来启动 Master 进程，Master 有足够的稳定性，不再需要使用 pm2 等进程守护模块。
2. 框架也提供了 egg-scripts 来支持线上环境的运行和停止。

```
$ npm i egg-scripts --save
添加 npm scripts 到 package.json：
{
  "scripts": {
    "start": "egg-scripts start --daemon",
    "stop": "egg-scripts stop"
  }
}
```

3. 启动应用`$ egg-scripts start --port=7001 --daemon --title=egg-server-showcase`

参数|功能
--|--
--port=7001 | 端口号，默认会读取环境变量 process.env.PORT，如未传递将使用框架内置端口 7001。
--daemon    | 是否允许在后台模式，无需 nohup。`若使用 Docker 建议直接前台运行`。
--env=prod  | 框架运行环境，默认会读取环境变量 process.env.EGG_SERVER_ENV， 如未传递将使用框架内置环境 prod。
--workers=2 | 框架 worker 线程数，默认会创建和 CPU 核数相当的 app worker 数，可以充分的利用 CPU 资源。
--title=egg-server-showcase |用于方便 ps 进程时 grep 用，默认为 egg-server-${appname}。
--framework=yadan |如果应用使用了自定义框架，可以配置 package.json 的 egg.framework 或指定该参数。
--ignore-stderr |忽略启动期的报错。
--https.key |指定 HTTPS 所需密钥文件的完整路径。
--https.cert|指定 HTTPS 所需证书文件的完整路径。

4. 所有 egg-cluster 的 Options 都支持透传，如 --port 等。
5. 更多参数可查看 egg-scripts 和 egg-cluster 文档。
6. 启动配置项也可以在config.{env}.js 中配置指定启动配置。

```
// config/config.default.js

exports.cluster = {
  listen: {
    port: 7001,
    hostname: '127.0.0.1',
    // path: '/var/run/egg.sock',
  }
}
path，port，hostname 均为 server.listen 的参数，egg-scripts 和 egg.startCluster 方法传入的 port 优先级高于此配置。
```

7. 停止命令`$ egg-scripts stop [--title=egg-server]`，该命令将杀死 master 进程，并通知 worker 和 agent 优雅退出。
支持以下参数：

```
--title=egg-server 用于杀死指定的 egg 应用，未传递则会终止所有的 Egg 应用。
你也可以直接通过 ps -eo "pid,command" | grep -- "--title=egg-server" 来找到 master 进程，并 kill 掉，无需 kill -9。
```

### 监控

#### Node.js 性能平台（alinode）

1. Node.js 性能平台全部功能免费提供给用户使用。
2. AliNode Runtime 可以直接替换掉 Node.js Runtime。

#### NSolid

### 前置代理模式（一般都需要反向代理）

1. `一般来说我们的服务都不会直接接受外部的请求，而会将服务部署在接入层之后，从而实现多台机器的负载均衡和服务的平滑发布，保证高可用。`
2. 在这个场景下，我们无法直接获取到真实用户请求的连接，从而无法确认用户的真实 IP，请求协议，甚至请求的域名。为了解决这个问题，框架默认提供了一系列配置项来让开发者配置，以便基于和接入层的约定（事实标准）来让应用层获取到真实的用户请求信息。


### docker

1. 基于eggjs修改增加`alinode`和nginx、https的支持。
2. [`直接在node镜像之上安装alinode，运行时一直不能选择alinode`].
3. 考虑基于[toomeefed/docker-alinode](https://github.com/toomeefed/docker-alinode/blob/master/4/slim/Dockerfile)进行更改：增加eggjs支持和nginx支持。

#### [eggjs/docker](https://github.com/eggjs/docker)

1. 官方Docker。
2. 没有使用alinode。
3. 没有nginx。

#### [aliyun-node/alinode-docker](https://github.com/aliyun-node/alinode-docker)

1. 非egg。
2. 没有nginx。
