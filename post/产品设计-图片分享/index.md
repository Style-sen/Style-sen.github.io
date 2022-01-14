# 图片分享


# 截屏

## 浏览器端

1. 最直接的方案是Canvas。
2. `1`将浏览器中当前DOM的内容渲染到Canvas中，（[niklasvh/html2canvas](https://github.com/niklasvh/html2canvas)），再将Canvas对应的imageData传到服务器解析。
3. `2`将DOM对应的HTML转化为SVG`不建议使用`。
4. 使用canvas也有一些限制.
5. 纯前端的解决方案对一些简单项目还行，复杂的还是依赖服务端更靠谱些.

## 服务端

1. 主流的互联网编程语言都有个实现的库，java有`selenium`，php有`imagegrabscreen`，python有`PIL`，nodejs的`phantomjs`和`puppeteer`，实现上也都比纯前端方案成熟稳定，效率也高。
2. 当然也有问题，比如不好处理异步请求渲染的内容等等。
3. 所以如果想要达到比较好的效果，前后端结合的方案靠谱，前端上传截屏位置大小信息和页面内容等信息，服务端截图，截图后再发回前端预览。

### phantomjs

1. [fzaninotto/screenshot-as-a-service](https://github.com/fzaninotto/screenshot-as-a-service)A simple screenshot web service powered by Express and PhantomJS. Forked from screenshot-app.
2. 参考[phantomjs实现服务端屏幕截图](https://bingozb.github.io/51.html).

### puppeteer

1. 和`phantomjs`对比，摘自[phantomjs与puppeteer简单对比(截图功能)](https://blog.csdn.net/a529625137/article/details/82761250).
2. 优于phantomjs
3. 基于[notadd/docker-puppeteer-chinese](https://github.com/notadd/docker-puppeteer-chinese) debain版本
4. 或者基于[截图的诱惑：Docker部署Puppeteer项目](https://www.jianshu.com/p/6a07fbd5b299) alpine版本
5. 注意：`puppeteer会出现截全图不全的情况`。[screenshot fullpage will not fully has full page content ](https://github.com/puppeteer/puppeteer/issues/3170).
6. 所以要使用[Puppeteer Full Page Screenshot](https://www.npmjs.com/package/puppeteer-full-page-screenshot),It takes multiple screenshots internally then merges them.他会`截取多张图片然后合并`。
```
const puppeteer = require("puppeteer");
const fullPageScreenshot = require("puppeteer-full-page-screenshot");
const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.setViewport({ width: 1920, height: 1080 });
await page.goto(url);
ctx.logger.debug(typeof(fullPageScreenshot.default));
await fullPageScreenshot.default(page, { path: "./page.png" });
await browser.close();
return;
```
7. `注意:不可能每响应一次请求，就创建一次Browser。`参考[使用 generic-pool 优化 puppeteer 并发问题](https://blog.guowenfh.com/2019/06/16/2019/puppeteer-pool/).

####  generic-pool 
1. 不可能每一次都启动和关闭一个 puppeteer 实例，generic-pool 就要出场了。

#### [使用手册](https://www.bookstack.cn/books/puppeteer-api-zh_CN)

##### page [class]

###### 等待
page.waitForNavigation(options)
page.waitFor(selectorOrFunctionOrTimeout[, options[, ...args]])
page.waitForSelector(selector[, options])
page.waitForXPath(xpath[, options])
page.waitForFunction(pageFunction[, options[, ...args]])

#### DOCKER

1. `FROM buildkite/puppeteer`


## 文件访问

1. 注：直接用nginx托管的静态文件，总存在`favicon.ico`请求，干扰微信小程序的下载。
2. 所以，需要使用原生框架渲染接口，参考[原生koa2实现静态资源服务器](https://chenshenhai.github.io/koa2-note/note/static/server.html).
