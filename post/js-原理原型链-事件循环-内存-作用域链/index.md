# JS-原理(原型链-事件循环-内存-作用域链-垃圾回收)


摘自[面试一定会问到的-js事件循环](https://juejin.im/post/5da742936fb9a04e223333ff)

# 线程

1. 执行中的线程。
    主线程：也就是` js 引擎执行的线程，这个线程只有一个`，页面渲染、函数处理都在这个主线程上执行。
    工作线程：也称`幕后线程`，这个线程可能存在于`浏览器或js引擎内`，与主线程是分开的，处理`文件读取、网络请求`等异步事件(**主要调用浏览器或node提供的API**)。
2. `js主线程它是有一个执行栈的`，**所有的js代码都会在执行栈里运行**,**栈去队列中取任务（方法（函数）），一开始就去macrotask去任务**。
3. 在执行代码过程中，如果遇到一些`异步代码(比如setTimeout,ajax,promise.then以及用户点击等操作),那么浏览器就会将这些代码放到另一个线程(在这里我们叫做幕后线程)中去执行`，在前端由浏览器底层执行，在 node 端由 libuv 执行，这个线程的执行不阻塞主线程的执行，主线程继续执行栈中剩余的代码。
4. 当`幕后线程（background thread）里的代码执行完成后(比如setTimeout时间到了，ajax请求得到响应),该线程就会将它的回调函数放到任务队列`（又称作事件队列、消息队列）中等待执行。而当主线程执行完栈中的所有代码后，它就会检查任务队列是否有任务要执行，如果有任务要执行的话，那么就将该任务放到执行栈中执行。如果当前任务队列为空的话，它就会一直循环等待任务到来。因此，这叫做事件循环。

# 任务队列

1. js是有`两个任务队列`的，一个叫做 Macrotask Queue(Task Queue) 大任务, 一个叫做 Microtask Queue 小任务.
2. Macrotask 常见的任务：

setTimeout
setInterval
setImmediate
I/O
用户交互操作，UI渲染

3. Microtask 常见的任务：

Promise(重点)
process.nextTick(nodejs)
Object.observe(不推荐使用)
4. **一次事件循环只执行处于 Macrotask 队首的任务，执行完成后，立即执行 Microtask 队列中的所有任务。**

# 事件循环

1. async/await 如何处理

```js
async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}
// 其实就是
async function async1() {
    console.log('async1 start');
    Promise.resolve(async2()).then(()=>console.log('async1 end'))
}
```

2. setTimeout(task,100) 后，其实只是确保这个任务，`会在100毫秒后进入macrotask队列，但并不意味着他能立刻运行`，可能当前主线程正在进行一个耗时的操作，也可能目前microtask队列有很多个任务，所以用 setTimeout 作为倒计时其实并不会保证准确。
3. js 核心还是同步阻塞的。
4. 对于 js 的异步事件，因为有事件循环机制，异步事件就是由事件驱动异步非阻塞的。所以 nodejs 适合处理大并发，因为有事件循环和任务队列机制，异步操作都由工作进程处理（libuv），js 主线程可以继续处理新的请求。缺点也很明显，因为是单线程，所以对计算密集型的就会比较吃力，不过可以通过集群的模式解决这个问题。

# 垃圾回收


