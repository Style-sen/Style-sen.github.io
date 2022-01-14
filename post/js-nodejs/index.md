# NodeJS概览及开发框架


## 安装

### 首先安装NVM

#### Ubuntu

1. 安装[nvm](https://github.com/creationix/nvm)

#### (win8及以上)

1. 安装[nvm-windows](https://github.com/coreybutler/nvm-windows)
2. win8以下系统

```js
在Windows的系统变量和用户变量中，均添加NVM_HOME和NVM_SYMLINK这两个名称的变量，前一个变量的值为nvm的安装路径，如C:\Dev\nvm，后一个变量的值为node.js的安装路径，如C:\Dev\nodejs。除此之外，还要确保系统变量PATH中，有%NVM_HOME%和%NVM_SYMLINK%这两个值，每个值的后面要加上英文的分号
```

```js
node_mirror: http://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

将上边两行放入settings.txt文件中。

### 然后安装NODE(会自动安装npm)

```js
nvm --version
nvm install 10.15.3
nvm use 10.15.3
```

```js
// 源管理
npm install -g nrm
nrm list
nrm use taobao //尽量不适用taobao源
```

注：windows还需安装[npm install -g node-gyp](https://github.com/nodejs/node-gyp#installation)和[npm install --global windows-build-tools](https://github.com/felixrieseberg/windows-build-tools)

### 使用yarn替换npm

`npm install yarn -g`

或者[Installation](https://yarnpkg.com/en/docs/install#debian-stable)

### 安装cnmp完全替代nmp(使用国外的项目不要使用cnpm，尽量不用)

```js
npm install cnpm -g --registry=https://registry.npm.taobao.org
```

### 问题

1. [electron打包出现 Error: Unresolved node modules: vue](https://blog.csdn.net/gaoxuaiguoyi/article/details/80708815)

## NPM和YARN使用

### 命令

1. [npm - 参考手册](https://juejin.im/entry/590fcd02a22b9d0058036856)

|NPM命令| 选项 |功能|YARN|
|--|--|--|--|
|npm info    |[name] |  查看包所有的版本信息 |
|npm ls         | [name] | 查看已安装的包的版本信息 |
|                       | [name] -g | 查看全局安装的包的版本信息 |
|npm view   | [name] versions | 查看包所有的版本信息 |
|                       | [name] version | 查看包最新的版本信息 |
|npm install -g | 升级全局的本地包 | yarn global add |
|npm update [name] | 升级某一个包 |
|npm install --unsafe-perm |设置为true以在运行程序包脚本时禁止UID / GID切换|
|npm install --save [--save-dev] |-S, --save: Package will appear in your dependencies;-D, --save-dev: Package will appear in your devDependencies;-O, --save-optional: Package will appear in your optionalDependencies.|
|npm install --production |添加了production  参数后将仅仅  安装  package.json 中dependencies 里面的包，不会安装devDependencies 里面的 ||
|npm audit | 允许开发人员分析复杂的代码，并查明特定的漏洞和缺陷|
|npm audit fix | 检测项目依赖中的漏洞并自动安装需要更新的有漏洞的依赖，而不必再自己进行跟踪和修复|
| npm audit fix --package-lock-only | 运行audit fix，但是只更新pkglock， 不更新node_modules|
| npm audit fix --only=prod | 只更新dependencies中安装的包，跳过devDependencies中的包|
| npm audit fix --dry-run --json | 运行命令，得到audit fix将会更新的内容，并且输出json格式的安装信息，但是并不真的安装更新|
| npm audit --json | 得到json格式的详细检测报告|

```js
安装github工程
npm install https://github.com/huixisheng/zepto-lazyload.git#branch
```

#### npx

参考[npx 使用教程](http://www.ruanyifeng.com/blog/2019/02/npx.html)

1. 调用项目内部安装的模块.一般来说，调用 `Mocha` ，只能在项目脚本`node-modules/.bin/mocha --version`和 package.json 的scripts字段里面。`npx macha --version`
2. 原理很简单，就是运行的时候，会到node_modules/.bin路径和环境变量$PATH里面，检查命令是否存在。`也可以调用系统命令`。
3. 除了调用项目内部模块，npx 还能避免全局安装的模块。
4. 如果想让 npx 强制使用本地模块，不下载远程模块，可以使用--no-install参数。如果本地不存在该模块，就会报错。
5. 如果忽略本地的同名模块，强制安装使用远程模块，可以使用--ignore-existing参数。
6. 例: 当前目录起一个http服务。

##### 项目的根目录下执行

```js
node-modules/.bin/mocha --version
```

### package.json

1. 作为描述文件，描述你的项目依赖哪些包。
2. 文件内容：

```js
    name              # 【必须】
    version           # 【必须】
    description       # 描述信息，有助于搜索
    main              # 入口文件，一般都是 index.js
    scripts           # 支持的脚本，默认是一个空的 test
    keywords          # 关键字，有助于在人们使用 npm search 搜索时发现你的项目
    author            # 作者信息
    license           # 默认是 MIT
    bugs              # 当前项目的一些错误信息，如果有的话
    dependencies      # 是一个对象，配置模块依赖的模块列表，key是模块名称，value是版本范围，版本范围是一个字符，可以被一个或多个空格分割
```

### npm问题

1. [sudo npm install 时，报错 permission denied 的解决方案](https://newsn.net/say/sudo-npm-install-permission.html)

## PM2

pm2是一个内置负载均衡的node.js应用进程管理器

`npm install -g pm2`

|命令|功能|
|--|--|
|pm2 start server.js --name | 启动一个应用，并指定名称 |
|pm2 list | 列出正在运行的应用 |
|pm2 stop [id/name] | 停止对应的应用 |
|pm2 delete [name] | 删除对应的应用 |
|pm2 reload [name] | 代码有更新，重载应用 |
|pm2 restart [name] |重启应用 |
|pm2 show [name] | 查看相应的应用详情|

## 内置对象

### [process](http://javascript.ruanyifeng.com/nodejs/process.html)

1. process对象是 Node 的一个全局对象，提供当前 Node 进程的信息。它可以在脚本的任意位置使用，不必通过require命令加载。该对象部署了EventEmitter接口。

|属性|描述|
|--|--|
|process.version|返回一个字符串，表示当前使用的 Node 版本，比如v7.10.0|
|process.env | 获取系统环境变量 |

### [Node.js EventEmitter](http://www.runoob.com/nodejs/nodejs-event.html)

```js
创建一个模块
import EventEmitter from 'events';
class someThing extends EventEmitter{
    a=null
    constructor (){
        //初始化
        super()

        this.a = new b();
        this.a.on("", ()=>{
            this.emit("",c);
        });
    }

    method (){
    }
}
export default someThing;
```

### http

## NODEJS工程

### 开始

```js
    npm init          # 可在当前目录下创建一个package.json文件
    npm init --yes    # 可跳过回答问题步骤
```

#### 自定义npm init行为

1. 参考[2018 年了，你还是只会 npm install 吗？](https://juejin.im/post/5ab3f77df265da2392364341#heading-0)
2. 定制 npm init 命令的实现方式也很简单，在 Home 目录创建一个 `.npm-init.js` 即可，该文件的 module.exports 即为 package.json 配置内容，需要获取用户输入时候，使用 prompt() 方法即可。
3. .npm-init.js 是一个常规的模块，意味着我们可以执行随便什么 node 脚本可以执行的任务。例如通过 fs 创建 README, .eslintrc 等项目必需文件，实现`项目脚手架`的作用。

### 目录结构

index.js
main
|----config.js

### `npm install`安装依赖

1. `npm install <package>` 除了简单的指定包名, package 还可以是一个指向有效包名的 `http url`、`git url`和`文件夹路径`。
2. [npm支持的git url格式](https://docs.npmjs.com/files/package.json#git-urls-as-dependencies)`<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]`

### 编写npm模块

1. 初始化`npm init`；
2. 创建入口文件;
3. 在`src`中编写源代码。
4. 在`examples`中编写示例。
5. 通过.npmignore来在npm publish的时候，忽略自己不想要的文件。
6. 放进git仓库中.
7. `注意`:[使用module.exports导出模块，内部定义的函数之间如何相互调用](https://www.imooc.com/wenda/detail/409466).第一种方法：所在文件就是一个模块，定义function之后，其作用域就是整个模块。最后再module.exports={}.第二种方法：若一开始就module.exports={}中写方法，不能用this,只能用exports。全局的this就是module.exports.
8. `注意`:[exports的用法:Node.js模块的接口设计模式](https://gywbd.github.io/posts/2014/11/using-exports-nodejs-interface-design-pattern.html)

#### 私有registry

1. 将自己的私有模块放在私有registry中。
2. [Verdaccio:A lightweight open source private npm proxy registry](https://verdaccio.org/)。
3. 可使用docker安装。

### 最佳实践[参考](https://juejin.im/post/5ab3f77df265da2392364341#heading-0)

1. 使用 `npm: >=5.1` 版本, 保持 package-lock.json 文件默认开启配置
2. 初始化：第一作者初始化项目时使用 `npm install <package>` 安装依赖包, 默认保存 ^X.Y.Z 依赖 range 到 package.json中; 提交 package.json, package-lock.json, 不要提交 node_modules 目录
3. 初始化：项目成员首次 checkout/clone 项目代码后，执行一次 npm install 安装依赖包
4. 不要手动修改 package-lock.json
5. 升级依赖包:
    升级小版本: 本地执行 npm update 升级到新的小版本
    升级大版本: 本地执行 `npm install <package-name>@<version>` 升级到新的大版本
    也可手动修改 package.json 中版本号为要升级的版本(大于现有版本号)并指定所需的 semver, 然后执行 npm install
    本地验证升级后新版本无问题后，提交新的 package.json, package-lock.json 文件
6. 降级依赖包:
    正确: `npm install <package-name>@<old-version>` 验证无问题后，提交 package.json 和 package-lock.json 文件
    错误: 手动修改 package.json 中的版本号为更低版本的 semver, 这样修改并不会生效，因为再次执行 npm install 依然会安装 package-lock.json 中的锁定版本
7. 删除依赖包:
    Plan A: `npm uninstall <package>` 并提交 package.json 和 package-lock.json
    Plan B: 把要卸载的包从 package.json 中 dependencies 字段删除, 然后执行 npm install 并提交 package.json 和 package-lock.json
8. 任何时候有人提交了 package.json, package-lock.json 更新后，团队其他成员应在 svn update/git pull 拉取更新后执行 npm install 脚本安装更新后的依赖包

### 语义化版本控制

1. 官方文档适用于任何软件管理[语义化版本 2.0.0](https://semver.org/lang/zh-CN/)
2. [npm 语义化版本控制](https://segmentfault.com/a/1190000018714929)

## 框架集合

查看流行框架，参考[nodeframework](http://nodeframework.com/)

### [Express](http://www.expressjs.com.cn/)    ***

1. Express是一个最小且灵活的Web应用程序框架，为Web和移动应用程序提供了一组强大的功能，它的行为就像一个中间件，可以帮助管理服务器和路由.
2. express可能是Node.js最流行的框架，还有许多其他流行的框架都是基于Express构建的。
3. 个人评价，express适合小型项目，不适合大型企业级项目，个人用用还可以，做为快速入门是个很好的选择，用过之后就可以考虑进入 koa 框架的道路.

#### 优点

* 几乎是Node.js Web中间件的标准.
* 简单，简约，灵活和可扩展.
* 快速开发应用程序.
* 完全可定制.
* 学习曲线低.
* 轻松集成第三方服务和中间件.
* 主要关注浏览器，模板和渲染集成开箱即用

#### 缺点

* 组织需要非常清楚，以避免在维护代码时出现问题
* 随着代码库大小的增加，重构变得非常具有挑战性
* 需要大量的手工劳动，因为您需要创建所有端点.

#### 性能的最佳实践

1. 使用gzip压缩
2. 不要使用同步功能
3. 正确记录（用于调试，使用特殊模块，如调试，应用程序活动使用winston或bunyan）
4. 使用try-catch或promises正确处理异常
5. 确保您的应用程序使用流程管理器自动重新启动，或使用systemd或upstartinit等系统
6. 在群集中运行您的应用。您可以通过启动进程集群来大大提高Node.js应用程序的性能
7. 缓存请求结果，以便您的应用不会重复操作以反复提供相同的请求
8. 使用负载均衡器运行它的多个实例并分配流量，如Nginx或HAProxy
9. 对静态资源使用反向代理。它可以处理错误页面，压缩，缓存，提供文件和负载平衡等
10. 更多[性能最佳实践](http://www.expressjs.com.cn/advanced/best-practice-performance.html).

#### [安全的最佳实践](http://www.expressjs.com.cn/advanced/best-practice-security.html)

### [KOA](https://koa.bootcss.com/)    ***

1. Koa 是一个新的 web 框架，由 Express幕后的原班人马打造，致力于成为web应用和API开发领域中的一个更小、更富有表现力、更健壮的基石。
2. 通过利用 async 函数，Koa帮你丢弃回调函数，并有力地增强错误处理.
3. Koa并没有捆绑任何中间件而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序

#### KOA优点

1. Koa提高了互操作性，健壮性，使编写中间件变得更加愉快。
2. 集成了大量的web API，但是没有绑定中间件
3. 非常轻量，核心的Koa模块只有大约2K行代码
4. 拥有非常好的用户体验
5. 通过try / catch更好地处理错误
6. 异步控制流，代码可读性更高

#### KOA缺点

1. Koa社区相对较小
2. 与Express风格的中间件不兼容(目前还有遇到与其他框架兼容的中间件)

#### 性能

1. Koa本身是一个非常轻量级的框架,可以构建具有出色性能的Web应用程序。代码可读性和维护性都相对较高
2. 当然一些性能的最佳实践也是必不可少的，例如：
3. 集群
4. 并行运行
5. 在代码中使用异步API
6. 保持代码小而轻
7. 以及使用gzip压缩 等等

#### 安全

1. Koa有大量的中间件，提供相应的功能.

### Sails   ****

1. 企业级框架
2. Sails 是基于 exrpess 的`大而全`的框架，MVC框架，旨在模拟熟悉的Ruby on Rails框架的MVC模式，但支持现代应用程序的需求。
3. 捆绑了一个`强大的ORM`，即Waterline。自动生成的REST API.

### [LoopBack]

1. 建立在 Express 基础上的企业级 Node.js 框架，只需编写少量代码就能创建动态端到端的 REST API，一致化的模型关系和对 API 访问的权限控制.

### [ThinkJS]    ****

1. 是国内360团队推出的一款面向未来开发的 Node.js 框架，整合了大量的项目最佳实践，让企业级开发变得如此简单、高效。框架底层基于 Koa 2.x 实现，兼容 Koa 的所有功能.

### [Eggjs]    *****

1. 是《阿里旗下产品》基于Node.js 和 Koa的一个Nodejs的企业级应用开发框架，它可以帮助开发团队及开发人员降低开发和维护成本。
2. Egg.js则是按照约定进行开发，奉行『约定优于配置』，具备提供基于Egg定制上层框架的能力、
3. 高度可扩展的插件机制、内置多进程管理、基于Koa开发，性能优异、框架稳定，测试覆盖率高、渐进式开发、开发成本和维护成本低等特点。

### [Hapi]

1. Hapi是基础功能相对丰富的框架。开发人员更专注于业务，而不是花时间构建基础架构。
2. 配置驱动的模式，区别于传统的web服务器操作。
3. 他还有比一个独特功能，能够在特定的IP上创建服务器，具有类似的功能onPreHandler。再需要的时候你可以拦截特地的请求做一些必要的操作

#### 好处

* 提供了一个强大的插件系统，允许您快速添加新功能和修复错误
* 可扩展的API
* 对请求处理有更深层次的控制。
* `创建(REST)api的最佳选择，提供了路由、输入、输出验证和缓存`
* 一次编写适配各端
* 详细的API参考和对文档生成的良好支持
* 与任何前端框架（如React，Angular和Vue.js）一起使用来创建单页面应用程序
* 基于配置的伪中间件
* 提供缓存，身份验证和输入验证
* 提供基于插件的扩展架构
* 提供非常好的企业插件，如joi，yar，catbox，boom，tv和travelogue

#### HAPI缺点

* 代码结构复杂
* 插件不兼容，只能使用指定的插件如：catbox joi boom tv good travelogue等
* 端点是手动创建的，必须手动测试
* 重构是手动的

#### HAPI性能

1. 2017年对Node框架的研究表明hapi相对于其他框架的表现最差

#### HAPI安全

1. hapi安全性主要依赖于插件
2. 插件选择

* Crumb反（XCSRF）验证插件。它适用于常规请求和CORS请求
* Joi：JavaScript对象的对象模式描述语言和验证器
* Hapi-rbac 用户的访问权限控制
* Blankie 足够灵活的白名单作机制
* Cryptiles 加密库

### [nest](https://github.com/nestjs/nest)

1. Nest是构建高效，可扩展的 Node.js Web 应用程序的框架。 它使用现代的 JavaScript 或 TypeScript（保留与纯 JavaScript 的兼容性），并结合 OOP（面向对象编程），FP（函数式编程）和FRP（函数响应式编程）的元素。
2. 在底层，Nest 使用了 Express，但也提供了与其他各种库的兼容，例如Fastify，可以方便地使用各种可用的第三方插件。
3. 近几年，由于 Node.js，JavaScript 已经成为 Web 前端和后端应用程序的「通用语言」，从而产生了像Angular、React、Vue等令人耳目一新的项目，这些项目提高了开发人员的生产力，使得可以快速构建可测试的且可扩展的前端应用程序。 然而，在服务器端，虽然有很多优秀的库、helper 和 Node 工具，但是它们都没有有效地解决主要问题 - 架构。
4. Nest 旨在提供一个开箱即用的应用程序体系结构，允许轻松创建高度可测试，可扩展，松散耦合且易于维护的应用程序。
5. 面向 AOP 编程
6. 支持 typeorm
7. Node.js 版的 spring
8. 构建微服务应用

