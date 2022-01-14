# Django静态文件


## 思路

1. Django提供 django.contrib.staticfiles 中间件来管理静态文件。

## 方法

### 配置

1. 将 django.contrib.staticfiles 包含进  INSTALLED_APPS；
2. 在配置文件中定义 STATIC_URL；

### 访问

#### 开发

1. 开发中，使用 runserver 并且 DEBUG=TRUE，可以直接访问；但是效率低而且不安全，不能用于生产环境。
#### 生产环境
