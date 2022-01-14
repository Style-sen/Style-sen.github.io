# PLAT QT 扩展模块 03 WEBCHANNEL



## 1. QWebChannel

1. QWebChannel 提供了在 Server（C++应用）和 client 端（HTML/JS）之间点对点的通信能力。
2. 通过向 client 端的 QWebChannel 发布 QObject 的 派生对象，进而实现在 client 端无缝读取来自 Qt 端的 公共插槽 和 QObject 的 属性值 和 方法。在整个通信过程中，无需任何手动序列化传入的参数。所有 Qt 端的 属性 更新，signal 触发，都会 自动且异步 更新到 client 端。
3. 必须引入 Qt 官方提供的`qwebchannel.js`,github，official 的 JS 库.
4. 在实现通信之前，必须实例化一个 QWebChannel 对象并传递一个用于传输功能的对象（称为 transport 对象）和一个回调函数。一旦 QWebChannel 完成实例化并 发布对象 变得可用时，将调用之前实例化时提供的回调函数。在回调函数被调用时，即表明通道建立。

