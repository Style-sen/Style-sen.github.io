# LUA常用模块


## 1. bit

1. LUA本身不支持位运算,所以需要bit模块。

### 1.1. 操作

```lua
bit.lshift(x, n)    --逻辑左移
bit.rshift(x, n)    --逻辑右移
bit.arshift(x, n)   --算术右移
bit.band()          --与
```

## 2. JSON

### 2.1. cjson

1. 需要手动编译[link](https://kyne.com.au/~mark/software/lua-cjson-manual.html#_installation)
2. 【要求编码格式必须是utf-8】;如果不是需要自己写，但是性能会降低[link](https://www.ancii.com/at5ndju40/)

## 3. websocket

1. lua-resty-websocket[link](https://github.com/openresty/lua-resty-websocket)
2. 发送超时（别的进程CPU占用过高，会导致超时），ngx.exit(444)会导致浏览器websocket异常断开（1006）。

### 3.1. API

```lua
send_text   --发送文本字符串(utf-8);
send_binary --1. 发送`byteArray`;2. 调用`send_frame(self, true, 0x2, data)`.
recv_frame  --超时为new设置的超时时间
server:new{timeout = 5000, max_payload_len = 65535}
```

## 4. lua-resty-aes[link](https://github.com/c64bob/lua-resty-aes)

## 5. lua-resty-core[link](https://github.com/openresty/lua-resty-core)

1. New FFI-based Lua API for ngx_http_lua_module and/or ngx_stream_lua_module

## 6. lua-resty-lock[link](https://github.com/openresty/lua-resty-lock)

1. Simple shm-based nonblocking lock API

## 7. lua-resty-session[link](https://github.com/bungle/lua-resty-session)

1. 安全、灵活的会话库

### 7.1. API

```lua
session:parse_cookie(cookie)    --Parses cookie and returns the data back as a table on success and nil and error on errors.
session:get_cookie()            --Returns the cookie from the request or nil if the cookie was not found
session.new(opts)
session:regenerate(flush, close)--重新生成一个session，并且重新设置Cookie
session:save(close)             --保持原状，不变
```

### 7.2. 原理

#### 7.2.1. save()

##### 7.2.1.1. 保存session并设置cookie

#### 7.2.2. session的field

##### 7.2.2.1. expires

###### 7.2.2.1.1. 超时的时间点

## 8. lua-resty-string[link](https://github.com/openresty/lua-resty-string)

### 8.1. String utilities and common hash functions for ngx_lua and LuaJIT

### 8.2. 核心文件

#### 8.2.1. sha1.lua

## 9. lua-resty-upload[link](https://github.com/openresty/lua-resty-upload)

### 9.1. Streaming reader and parser for HTTP file uploading based on ngx_lua cosocket

### 9.2. 有权限的问题

## 10. LuaSQLite 3[link](http://lua.sqlite.org/index.cgi/doc/tip/doc/lsqlite3.wiki)

### 10.1. a Lua 5.1 to 5.3 wrapper for the SQLite3 library

### 10.2. To use this module you need the SQLite3 library (DLL or .so)

## 11. ffi

1. 集成在luajit中
2. 编译器在查找动态库所在的路径的时候其中一个环节就是在 LD_LIBRARY_PATH 这个环境变量中的所有路径进行查找

### 11.1. API

```lua
ffi.load(name [,global])    --name:z就是libz.so;global:ture，这个库符号被加载到一个全局命名空间
```

