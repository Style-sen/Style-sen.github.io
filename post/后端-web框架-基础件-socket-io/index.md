# 后端-web框架-基础件-socket.io


Socket.IO 是一个基于 Node.js 的实时应用程序框架，在即时通讯、通知与消息推送，实时分析等场景中有较为广泛的应用。

## Server

### INIT

```
const io = require('socket.io')(server, options);
```

### 命名空间(Namespaces)

1. 表示在由路径名（例如：/chat）标识的给定范围内连接的`socket池`。
2. 客户端始终连接到`/（主命名空间）`，然后可能连接到其他命名空间（使用相同的底层连接）。

#### 使用场景

1. 设置一个管理命名空间，只有认证的用户可以访问。

```
const adminNamespace = io.of('/admin');
```

2. 多个租户的话，可以为每个租户动态创建一个命名空间。

```
const workspaces = io.of(/^\/\w+$/);
```

#### 默认命名空间（/）

1. This namespace is identified by io.sockets or simply io。

```
// the following two will emit to all the sockets connected to `/`
io.sockets.emit('hi', 'everyone');
io.emit('hi', 'everyone'); // short form
```

#### 属性

##### namespace.name (字符串)

1. 命名空间标识符

##### namespace.connected（对象）

1. 连接到此命名空间的Socket对象的`哈希`，由id索引。

##### namespace.adapter（适配器）

1. 该Adapter用于命名空间。在使用Adapter基于Redis的时候很有用，因为它暴露了管理集群中的套接字和房间的方法。
注意：可以访问主命名空间的适配器io.of('/').adapter。

#### 方法

##### namespace.to（room）

1. 为随后的事件发射设置一个修饰符，事件只会广播到已经加入room给定的客户端room。
2. 要发射到多个房间，可以to多次打电话。
3. **返回：Namespace方便链式操作**

##### namespace.in（room）

1. namespace.to（room）的同义词。


##### namespace.emit（eventName [，... args]）

向所有连接的客户端发出事件。

##### namespace.clients（callback）

1. 获取连接到此命名空间的客户机ID（跨所有节点（如果适用））。
2. **和`to`与`in`配合使用，可以获取指定room的连接socket**。

```
const io = require('socket.io')();
io.of('/chat').clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [PZDoMHjiu8PYfRiKAAAF, Anw2LatarvGVVXEIAAAD]
});
```
获取命名空间中所有客户端的一个示例：
```
io.of('/chat').in('general').clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [Anw2LatarvGVVXEIAAAD]
});
```
与广播一样，默认是默认命名空间（'/'）的所有客户端：
```
io.clients((error, clients) => {
  if (error) throw error;
  console.log(clients); // => [6em3d4TJP8Et9EMNAAAA, G5p55dHhGgUnLUctAAAB]
});
```

### Rooms

1. Namespace内部, 可以定义任意的频道成为“Rooms”sockets可以join和leave.
2. 在给一小部分socket广播数据时比较有用。
3. `使用场景：将用户ID作为room，将socket加入进去，向用户发送；将设备sn作为room，将socket加入进去，向设备发送。`此时，可以建立对应映射关系，省去自己维护。

#### API

##### 加入Room

1. 一个socket自动加入以socketi#id命名的room.

```
io.on('connection', socket => {
  socket.join('some room');
});
```

##### 广播数据（to OR in）

1. 一个socket在多个room中也只会广播一条。
2. 向一个room中广播数据，不包括发送者。

```
io.to('some room').emit('some event');
io.to('room1').to('room2').to('room3').emit('some event');    // 同时向多个room广播
```

##### 离开Room

1. 断开连接，自动离开。

##### `从外部发送信息(socket.io-redis + socket.io-emitter)` ******

1. 通过redis作为信息通道。

```
//集成redis adapter
const io = require('socket.io')(3000);
const redis = require('socket.io-redis');
io.adapter(redis({ host: 'localhost', port: 6379 }));
//外部的任何进程
const io = require('socket.io-emitter')({ host: '127.0.0.1', port: 6379 });
setInterval(function(){
  io.emit('time', new Date);
}, 5000);
```

### Socket

## socket

1. A Socket是与浏览器客户端进行交互的基本类。A Socket属于某个Namespace（默认情况下/），并使用底层Client进行通信。
2. Socket类继承自EventEmitter。

### 属性

#### socket.id（串）

1. 会话的唯一标识符，来自底层Client。

#### socket.rooms

2. 在socket.join('room')之后。

#### socket.client（客户）

3. 对基础Client对象的引用。

#### socket.conn（engine.Socket）

4. 引用底层Client传输连接（engine.io Socket对象）。这允许访问IO传输层，其仍然（主要）抽象实际的TCP / IP套接字。

#### socket.request

5. 一个getter代理，返回引用request底层引擎的引用Client。用于访问请求头，如Cookie或User-Agent。

#### socket.handshake

握手细节：
```
{
  headers: /* the headers sent as part of the handshake */,
  time: /* the date of creation (as string) */,
  address: /* the ip of the client */,
  xdomain: /* whether the connection is cross-domain */,
  secure: /* whether the connection is secure */,
  issued: /* the date of creation (as unix timestamp) */,
  url: /* the request URL string */,
  query: /* the query object */
}
```
### 方法

#### socket.join（room [，callback]）

将客户端添加到room，并且可选地启动带有err签名的回调（如果有）。

#### socket.leave（room [，callback]）

1. 从中删除客户端room，并可选地启动带有err签名的回调（如果有）。
2. 断开后房间自动关闭。

## CLIENT

#### 使用Websocket协议

```
ws://[IP]:[PORT]/socket.io


```

#### 使用socketio库

```
io('http://[IP]:[PORT]/[命名空间]')
```

