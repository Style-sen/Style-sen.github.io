# PLAT-LINUX-应用-网络编程


## 1. UDP

### 1.1. 原理分析

1. 现象：使用UDP发送一包数据给服务端，收到一包ICMP报文（Port unreachable），由服务端返回。
2. 原因：接收UDP报文的服务器对应的端口没有开启UDP服务器。并不是端口没有开启服务，而是没有开启UDP服务，如果开启了TCP服务，照样也会回port unreachable。
3. [Linux内核原理](https://blog.csdn.net/mrpre/article/details/43451775)
