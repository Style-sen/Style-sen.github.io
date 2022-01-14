# 微服务架构笔记-网络协议


## http

1. HTTP协议是基于请求/响应模式的，因此只要服务端给了响应，本次HTTP连接就结束了。
2. HTTP请求和HTTP响应，都是通过TCP连接这个通道来回传输的。长连接是指的TCP连接，而不是HTTP连接。
3. TCP连接才有真正的长连接（目的是重复使用，多个Http请求复用同一个TCP连接）和短连接这一说。

### 消息结构

1. 客户端发送一个HTTP请求到服务器的请求消息包括以下格式：请求行（request line）、请求头部（header）、空行和请求数据四个部分组成。
2. HTTP响应也由四个部分组成，分别是：状态行、消息报头、空行和响应正文。
3. [HTTP头部信息解释分析(详细整理)](https://www.cnblogs.com/jiangxiaobo/p/5499488.html)

### 头部信息

应答头|	请求说明 | 应答说明
--|--|--
Allow	|服务器支持哪些请求方法（如GET、POST等）。
Connection | close（告诉WEB服务器或者代理服务器，在完成本次请求的响应 后，断开连接，不要等待本次连接的后续请求了）keepalive（告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求）| close（连接已经关闭）keepalive（连接保持着，在等待本次连接的后续请求）。 Keep-Alive：如果浏览器请求保持连接，则该头部表明希望 WEB 服务器保持连接多长时间（秒）。 例如：Keep-Alive：300 
Content-Encoding	|文档的编码（Encode）方法。只有在解码之后才可以得到Content-Type头指定的内容类型。利用gzip压缩文档能够显著地减少HTML文档的下载时间。Java的GZIPOutputStream可以很方便地进行gzip压缩，但只有Unix上的Netscape和Windows上的IE 4、IE 5才支持它。因此，Servlet应该通过查看Accept-Encoding头（即request.getHeader("Accept-Encoding")）检查浏览器是否支持gzip，为支持gzip的浏览器返回经gzip压缩的HTML页面，为其他浏览器返回普通页面。
Content-Length	|表示内容长度。只有当浏览器使用持久HTTP连接时才需要这个数据。如果你想要利用持久连接的优势，可以把输出文档写入 ByteArrayOutputStream，完成后查看其大小，然后把该值放入Content-Length头，最后通过byteArrayStream.writeTo(response.getOutputStream()发送内容。
Content-Type	|表示后面的文档属于什么MIME类型。Servlet默认为text/plain，但通常需要显式地指定为text/html。由于经常要设置Content-Type，因此HttpServletResponse提供了一个专用的方法setContentType。
Date	|当前的GMT时间。你可以用setDateHeader来设置这个头以避免转换时间格式的麻烦。
Expires	|应该在什么时候认为文档已经过期，从而不再缓存它？
Last-Modified	|文档的最后改动时间。客户可以通过If-Modified-Since请求头提供一个日期，该请求将被视为一个条件GET，只有改动时间迟于指定时间的文档才会返回，否则返回一个304（Not Modified）状态。Last-Modified也可用setDateHeader方法来设置。
Location	|表示客户应当到哪里去提取文档。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。
Refresh	|表示浏览器应该在多少时间之后刷新文档，以秒计。除了刷新当前文档之外，你还可以通过setHeader("Refresh", "5; URL=http://host/path")让浏览器读取指定的页面。 注意这种功能通常是通过设置HTML页面HEAD区的＜META HTTP-EQUIV="Refresh" CONTENT="5;URL=http://host/path"＞实现，这是因为，自动刷新或重定向对于那些不能使用CGI或Servlet的HTML编写者十分重要。但是，对于Servlet来说，直接设置Refresh头更加方便。 注意Refresh的意义是"N秒之后刷新本页面或访问指定页面"，而不是"每隔N秒刷新本页面或访问指定页面"。因此，连续刷新要求每次都发送一个Refresh头，而发送204状态代码则可以阻止浏览器继续刷新，不管是使用Refresh头还是＜META HTTP-EQUIV="Refresh" ...＞。 注意Refresh头不属于HTTP 1.1正式规范的一部分，而是一个扩展，但Netscape和IE都支持它。
Server	|服务器名字。Servlet一般不设置这个值，而是由Web服务器自己设置。
Set-Cookie	|设置和页面关联的Cookie。Servlet不应使用response.setHeader("Set-Cookie", ...)，而是应使用HttpServletResponse提供的专用方法addCookie。参见下文有关Cookie设置的讨论。
WWW-Authenticate	|客户应该在Authorization头中提供什么类型的授权信息？在包含401（Unauthorized）状态行的应答中这个头是必需的。例如，response.setHeader("WWW-Authenticate", "BASIC realm=＼"executives＼"")。 注意Servlet一般不进行这方面的处理，而是让Web服务器的专门机制来控制受密码保护页面的访问（例如.htaccess）。

## Server-Sent Events(简称SSE)

1. SSE是一种能让浏览器通过HTTP连接自动收到服务器端更新的技术，SSE EventSource 接口被W3C制定为HTML5的一部分。
2. 单向。

## websocket

1. 双向。

## HTTP状态码



## 术语

### 轮询

1. 客户端起一个`定时器`，定时发起请求向服务端请求数据。
2. 服务端接到请求之后马上返回响应信息并关闭连接。
3. `优点`：后端简单。
4. `缺点`：浪费带宽和服务器资源。
5. 场景：不适合客户端数量太多的情况。

### 长轮询

1. 客户端发起请求，请求返回结束后，立即发起第二次请求。
2. 服务端接到请求后hold住连接，直到有新消息才返回响应信息或等`超时`返回，关闭连接。
3. `优点`：在无消息的情况下不会频繁地请求，耗费资源小。
4. `缺点`：服务器hold住连接会消耗资源，返回数据顺序无保证，难于管理维护。
5. 场景：WebQQ网页扫码登录。不适合客户端数量太多的情况。

### 长连接

1. 客户端与服务端建立长连接，此连接不会断开，服务端随时可向客户端推送消息。（websocket, SSE）
2. `优点`：消息即时到达，不发无用请求；管理起来也相对方便。
3. `缺点`：服务器维护一个长连接会增加开销。
