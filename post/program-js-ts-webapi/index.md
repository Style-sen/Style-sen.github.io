# WebApi


1. WebAPI指一组设备兼容套件和访问接口，它允许Web应用及其内容访问设备硬件（比如电池状态或设备振动器），同时也可以获取设备上的数据（比如日历或联系人等信息）。通过这些API，我们希望对Web应用进行扩展，实现过去只有专有平台才可以实现的功能。
2. 在使用 JavaScript 编写 Web 代码时，有许多 API 可供调用。[这里](https://developer.mozilla.org/zh-CN/docs/Web/API)是所有接口（即对象类型）的列表，你可以在开发网站或 Web 应用程序时使用它们。

## 1. F

### 1.1. [FileReader](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)

FileReader 对象允许Web应用程序异步读取存储在用户计算机上的文件（或原始数据缓冲区）的内容，使用 File 或 Blob 对象指定要读取的文件或数据。

其中File对象可以是来自用户在一个<input>元素上选择文件后返回的FileList对象,也可以来自拖放操作生成的 DataTransfer对象,还可以是来自在一个HTMLCanvasElement上执行mozGetAsFile()方法后返回结果。

重要提示： FileReader仅用于以安全的方式从用户（远程）系统读取文件内容 它不能用于从文件系统中按路径名简单地读取文件。 要在JavaScript中按路径名读取文件，应使用标准Ajax解决方案进行服务器端文件读取，如果读取跨域，则使用CORS权限。

Note: 此特性在 Web Worker 中可用。

### 1.2. FileReaderSync

FileReaderSync接口允许以同步的方式读取File或Blob对象中的内容。

该接口只在workers里可用,因为在主线程里进行同步I/O操作可能会阻塞用户界面。

## 2. [WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)

### 2.1. 构造函数

```js
var ws = new WebSocket('ws://localhost:8080');
```

### 2.2. 属性

#### 2.2.1. readyState返回实例对象的当前状态

1. 共有四种：

    * CONNECTING：值为0，表示正在连接。
    * OPEN：值为1，表示连接成功，可以通信了。
    * CLOSING：值为2，表示连接正在关闭。
    * CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

#### 2.2.2. webSocket.onopen

1. 实例对象的onopen属性，用于指定连接成功后的回调函数。

  ```js
  ws.onopen = function () {
    ws.send('Hello Server!');
  }
  ```

2. 如果要指定多个回调函数，可以使用addEventListener方法。

  ```js
  ws.addEventListener('open', function (event) {
    ws.send('Hello Server!');
  });
  ```

#### 2.2.3. webSocket.onclose

1. 实例对象的onclose属性，用于指定连接关闭后的回调函数。
```
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};
ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```
#### 2.2.4. webSocket.onmessage

1. 实例对象的onmessage属性，用于指定收到服务器数据后的回调函数。
```
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};
ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```
2. 注意，服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）。
```
ws.onmessage = function(event){
  if(typeOf event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```
3. 除了动态判断收到的数据类型，也可以使用binaryType属性，显式指定收到的二进制数据类型。
```
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```

#### 2.2.5. webSocket.send()

1. 实例对象的send()方法用于向服务器发送数据。
```
发送文本的例子。

ws.send('your message');
发送 Blob 对象的例子。

var file = document
  .querySelector('input[type="file"]')
  .files[0];
ws.send(file);
发送 ArrayBuffer 对象的例子。

// Sending canvas ImageData as ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```
#### 2.2.6. webSocket.bufferedAmount

实例对象的bufferedAmount属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。
```
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```
#### 2.2.7. webSocket.onerror

实例对象的onerror属性，用于指定报错时的回调函数。
```
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});
```

### 2.3. 排错

1. 参考[WebSocket 经常断开原因，解决办法：心跳机制防止自动断开连接](https://blog.csdn.net/cai4561/article/details/106809244).

```
ws.onclose = function (e) {
  console.log('websocket 断开: ' + e.code + ' ' + e.reason + ' ' + e.wasClean)
  console.log(e)
}
```

1. e 是 `CloseEvent` 对象。
2. CloseEvent.code: code是错误码，是整数类型
3. CloseEvent.reason: reason是断开原因，是字符串
4. CloseEvent.wasClean: wasClean表示是否正常断开，是布尔值。一般异常断开时，该值为false。
