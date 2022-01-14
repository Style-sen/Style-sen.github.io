# 微服务架构笔记-中间件-CELERY


## 经验

1. 跑celery的容器和跑APP的容器最好分开，以分开数据库session，防止session混合。
