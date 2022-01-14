# Nginx


## 1. 配置

### 1.1. 配置文件`nginx.conf`

```yml

# 全局块:配置影响nginx全局的指令
user nobody;    #配置用户或者组，默认为nobody nobody。
worker_processes 1;    #允许生成的进程数，默认为1

error_log logs/error.log;    #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
# error_log logs/error.log notice;
# error_log logs/error.log info;

pid logs/nginx.pid;    #指定nginx进程运行文件存放地址
worker_rlimit_nofile 65535;  #指定进程可以打开的最大描述符：数目

# events块：配置影响nginx服务器或与用户的网络连接
events {
	accept_mutex on;   #设置网路连接序列化，防止惊群现象（一个网路连接到来，多个睡眠的进程被同事叫醒，但只有一个进程能获得链接，这样会影响系统性能）发生，默认为on
	multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    # use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
	worker_connections 1024;    #最大连接数，默认为512
    keepalive_timeout 60;  # keepalive超时时间
    open_file_cache max=65535 inactive=60s;    #这个将为打开文件指定缓存，默认是没有启用的，max指定缓存数量，建议和打开文件数一致，inactive是指经过多长时间文件没被请求后删除缓存。
    open_file_cache_valid 80s;  #这个是指多长时间检查一次缓存的有效信息。
}

# http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。
http {

    include       mime.types;    #文件扩展名与文件类型映射表
    default_type  application/octet-stream;     #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';    # 自定义日志格式$remote_addr 与 $http_x_forwarded_for 用以记录客户端的ip地址；$remote_user ：用来记录客户端用户名称；$time_local ： 用来记录访问时间与时区；$request ： 用来记录请求的url与http协议；$status ： 用来记录请求状态；成功是200；$body_bytes_s ent ：记录发送给客户端文件主体内容大小；$http_referer ：用来记录从那个页面链接访问过来的；$http_user_agent ：记录客户端浏览器的相关信息；

    #access_log  logs/access.log  main;    #combined为日志格式的默认值

    sendfile        on;    #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
	sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    #tcp_nopush     on;
    client_max_body_size 1m;  # 设置客户端请求体的最大值，默认是1M
    client_body_buffer_size；    # Nginx分配给请求数据的Buffer大小。小于client_body_buffer_size直接在内存中高效存储。如果大于client_body_buffer_size小于client_max_body_size会存储临时文件，临时文件一定要有权限。如果追求效率，就设置 client_max_body_size client_body_buffer_size相同的值，这样就不会存储临时文件，直接存储在内存了。

    #keepalive_timeout  0;
    keepalive_timeout  65;    #连接超时时间，默认为75s，可以在http，server，location块。
	upstream mysvr {       # 配置负载均衡
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;      #热备
    }
    error_page 404 https://www.baidu.com;     #错误页

    #gzip  on;
    # server块：配置【虚拟主机】的相关参数，一个http中可以有【多个server】
    server {
        keepalive_requests 120; #单连接请求上限次数。

        listen       80;    # 监听端口
        server_name  localhost;    # 监听地址

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        #location块：配置请求的路由，以及各种页面的处理情况。
        location / {    #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
            root   html;    # 设置根目录
            index  index.html index.htm;    # 设置默认页

            # 使用文件夹浏览功能时配置
            autoindex on;                            //开启目录浏览功能；
            autoindex_exact_size off;            //关闭详细文件大小统计，让文件大小显示MB，GB单位，默认为b；
            autoindex_localtime on;              //开启以服务器本地时区显示文件修改日期！
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        location ~ \.php$ {
            proxy_pass   http://127.0.0.1;    # 代理
        }
        location / {
            proxy_pass http://fe:80;
            index index.html;
        } # 反向代理可以用于前端页面的代理【在前端项目中，最好不要写死接口的域名，最好使用反向代理。写死之后在部署的时候，尤其端口转发部署的时候，有坑。】
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;    # 拒绝的IP all
		#    allow all;    # 允许的IP
        #}
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
         location ~* .(js)$ {
             add_header  Content-Type 'text/javascript;charset=utf-8';    # 其中匹配所有.js文件，给其responent header的Content-Type值赋值为"text/javascript;charset=utf-8"。
         }
    #}
}
```

### 1.2. 其他

1. [在nginx.conf配置文件中http选项配置多个serve即可实现多虚拟主机](http://www.nginx.cn/doc/example/virtualhost.html)

#### 1.2.1. http配置

##### 1.2.1.1. uwsgi_ignore_client_abort

1. Determines whether the connection with a uwsgi server should be closed when a client closes the connection without waiting for a response.

#### 1.2.2. 超时配置

##### 1.2.2.1. keepalive_timeout

1. HTTP 有一个 KeepAlive 模式，它告诉 webserver 在处理完一个请求后保持这个 TCP 连接的打开状态。若接收到来自客户端的其它请求，服务端会利用这个未被关闭的连接，而不需要再建立一个连接。
2. 一个http产生的tcp连接在传送完最后一个响应后，还需要hold住keepalive_timeout秒后，才开始关闭这个连接。
3. keep-alive并不是免费的午餐,长时间的tcp连接容易导致系统资源无效占用。配置不当的keep-alive，有时比重复利用连接带来的损失还更大。所以，正确地设置keep-alive timeout时间非常重要。

##### 1.2.2.2. client_body_timeout

1. 指定客户端与服务端建立连接后发送 request body 的超时时间。如果客户端在指定时间内没有发送任何内容，Nginx 返回 HTTP 408（Request Timed Out）。

##### 1.2.2.3. client_header_timeout

1. 客户端向服务端发送一个完整的 request header 的超时时间。如果客户端在指定时间内没有发送一个完整的 request header，Nginx 返回 HTTP 408（Request Timed Out）。

##### 1.2.2.4. send_timeout

1. 服务端向客户端传输数据的超时时间。

##### 1.2.2.5. lingering_timeout

1. TCP连接关闭时的SO_LINGER延时设置，默认5s

##### 1.2.2.6. resolver_timeout

1. 域名解析超时，默认30s

##### 1.2.2.7. send_timeout

1. 发送数据至客户端超时, 默认60s, 如果连续的60s内客户端没有收到1个字节, 连接关闭

##### 1.2.2.8. proxy_connect_timeout

1. nginx与upstream server的连接超时时间， 默认60s

##### 1.2.2.9. proxy_read_timeout

1. nginx接收upstream server数据超时, 默认60s, 如果连续的60s内没有收到1个字节, 连接关闭

##### 1.2.2.10. proxy_send_timeout

1. nginx发送数据至upstream server超时, 默认60s, 如果连续的60s内没有发送1个字节, 连接关闭

## 2. 命令

|命令|功能|
|--|--|
|nginx -s reload|重载配置文件|


## 3. 管理

### 3.1. 查看当前连接数

`netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'`

## 4. 扩展

### 4.1. [Tengine](http://tengine.taobao.org/documentation_cn.html)

Tengine是由淘宝网发起的Web服务器项目。它在Nginx的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。Tengine的性能和稳定性已经在大型的网站如淘宝网，天猫商城等得到了很好的检验。它的最终目标是打造一个高效、稳定、安全、易用的Web平台。

### 4.2. apache

`/etc/apache2/sites-enabled/`中存储虚拟主机配置文件。

```
<VirtualHost *:80>
	# The ServerName directive sets the request scheme, hostname and port
	ServerName www.test.cn
    ServerAdmin webmaster@localhost
	DocumentRoot /var/www/public
	ServerAlias test.cn
	<Directory "/var/www/public">
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

`/etc/a[acje2/sites-available/`中存储指向上边目录中配置文件中的软连接。


### 4.3. 主机记录

| 主机记录类型	| 说明 |
| -- | -- |
| www	| 将域名解析为www.example.com，填写www |
| @	| 将域名解析为example.com（不带www），填写@或者不填写 |
| mail	| 将域名解析为mail.example.com，通常用于解析邮箱服务器 |
| *	| 泛解析，所有子域名均被解析到统一地址（除单独设置的子域名解析）  |
| 二级域名	| 如：mail.example.com或abc.example.com，填写mail或abc  |
| 手机网站	| 如：m.example.com，填写m |

### 4.4. 注意

`配置域名之后，需要到域名控制处增加解析配置`

## 5. HTTP 状态码

### 5.1. 499

#### 5.1.1. nginx

1. `client has closed connection`,客户端关闭连接。一般是nginx主动拒绝，如果客户端重连次数过快。
2. 可以更改nginx配置解决:`proxy_ignore_client_abort on;`，但是这样不安全。

