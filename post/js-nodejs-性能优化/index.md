# NodeJS-性能


参考[你不知道的Node.js性能优化](https://zhuanlan.zhihu.com/p/50055740)
参考[Node.js 调试指南](https://www.bookstack.cn/read/node-in-debugging/README.md)

## 使用最新版的NodeJS

1. 对于生产环境而言，Node.js 官方推荐使用最新的 LTS 版本。

## 使用`fast-json-stringify`加速`JSON`序列化

## 提升 Promise 的性能

## 正确地编写异步代码

## 优化 V8 GC

## 正确地使用 Stream

## C++ 扩展一定比 JavaScript 快吗？

## 使用 node-clinic 快速定位性能问题

## 使用 alinode 监控

### 实战

1. 使用`docker+nginx+alinode+egg`部署应用。
2. 用户反应接口慢，alinode上显示CPU、内存、磁盘的负荷并不是很高。`在docker内部，使用top发现内存占用很高`。

### 设置告警

```python
1. 注意：`必须设置告警`；这样才能及时发现问题，并保存事故现场。
2. 截图ECS监控图；
3. 截图alinode监控图；
4. 保存alinode堆快cpu profile等性能数据文件；
5. 保存应用服务器的日志文件，查看`top`信息。
6. 保存应用的日志文件。
```

### 异常日志

1. 首先将异常解决掉。

### 诊断内存泄漏

1. 参考[如何分析 Node.js 中的内存泄漏](https://zhuanlan.zhihu.com/p/25736931) .
2. 参考[快速定位线上 Node.js 内存泄漏问题](https://yq.aliyun.com/articles/587187).
3. 参考[Node应用内存泄漏分析方法论与实战](https://github.com/alibaba/beidou/blob/master/packages/beidou-docs/articles/node-memory-leak.md).
4. 内存泄漏（Memory Leak）指由于`疏忽或错误造成程序未能释放已经不再使用的内存的情况`。如果内存泄漏的位置比较关键，那么随着处理的进行可能持有越来越多的无用内存，这些无用的内存变多会引起服务器响应速度变慢，严重的情况下导致内存达到某个极限（可能是进程的上限，如 v8 的上限；也可能是系统可提供的内存上限）会使得应用程序崩溃。

### CPU分析

1. [node进程cpu 100%问题排查](https://my.oschina.net/AviorAlong/blog/3132684).
2. [Node.js 应用故障排查手册 —— Node.js 性能平台使用指南](https://segmentfault.com/a/1190000018752005)
3. [Node使用火焰图优化CPU爆涨](https://zhuanlan.zhihu.com/p/76250769)

## 压测

### [alexfernandez/loadtest](https://github.com/alexfernandez/loadtest)


