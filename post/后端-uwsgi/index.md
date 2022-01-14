# 后端-uWSGI


## 配置

1. 默认情况下，uWSGI会在第一个启动的进程中加载整个应用，并在加载完毕之后，会将自己多次fork()。这是常见的Unix模式，这能够显著降低应用的内存使用量，允许使用一些有趣的技巧，同时对于某些语言来说，这也会给你带来一些令人头疼的问题。

`--lazy-apps`

### 超时

## 注意事项

1. uWSGI不使用--lazy-apps的过程中，使用`SQLachemy`很容易出现`Mysql serve has gone`的问题，会与多次fork主进程，依赖主进程资源有关。使用--lazy-apps之后，问题大大改善，但是在【一段访问】前期（根据访问创建多进程）还是会出现，访问稳定之后，问题消失。

## 问题

### uwsgi_response_writev_headers_and_body_do(): Broken pipe [core/writer.c 306] OSError: write error

1. IO报错；
2. This can happen when NGINX started a request to uWSGI but uWSGI took too long to respond, then NGINX closes the connection to uWSGI. When uWSGI finally finishes, it tries to give it's response back to NGINX, but NGINX closed the connection earlier, so then uWSGI throws an I/O error.


