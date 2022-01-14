# 后端-web框架-Flask


## 配置的最佳实践

### 变量的配置

1. 通过调用自定义config.py文件中config字典，可以得到一个类，这个类里面定义的都是类变量，这些变量就是自定义的一些配置项。
2. 敏感信息通过读取环境变量获得。
3. 环境变量可以在Docker部署的时候确定，即docker-compose.yml，此文件不要放入到版本控制中。

```
import os

BASEDIR = os.path.abspath(os.path.dirname(__file__))

class Config:
    """base config"""
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'secret key'
    SQLALCHEMY_COMMIT_ON_TEARDOWN = True
    SQLALCHEMY_TRACK_MODIFICATIONS = True

class ProductionConfig(Config):
    """运行环境配置"""
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'sqlite:///' + os.path.join(BASEDIR, 'data.sqlite')
......

config = {
    'development': DevelopmentConfig,
    'testing': ProductionConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}
```

```
app = Flask(__name__)
app.config.from_object(config['testing'])
```

#### 具体的变量

```python
app.config['JSON_AS_ASCII'] = False   # 让jsonify返回的json串支持中文显
```

### 日志的配置

1.  log配置，实现日志自动按日期生成日志文件

```
def make_dir(make_dir_path):
    path = make_dir_path.strip()
    if not os.path.exists(path):
        os.makedirs(path)
    return path


log_dir_name = "flask_logs"
log_file_name = ('logger-' +
                 time.strftime('%Y-%m-%d', time.localtime(time.time())) +
                 '.log')
log_file_folder = '/var/log' + os.sep + log_dir_name
make_dir(log_file_folder)
log_file_str = log_file_folder + os.sep + log_file_name
log_level = logging.WARNING
handler = logging.FileHandler(log_file_str, encoding='UTF-8')
handler.setLevel(log_level)
logging_format = logging.Formatter('%(asctime)s - %(levelname)s - %(filename)s \
- %(funcName)s - %(lineno)s - %(message)s')
handler.setFormatter(logging_format)

app = Flask(__name__)

app.logger.addHandler(handler)

```

2. 需要一个定时任务，定时清掉日志。

### 应用工厂 App Factory

1. Flask官方建议采用工厂的模式来创建应用

```
from flask import Flask
from flask_mail import Mail
from flask_sqlalchemy import SQLAlchemy
from werkzeug.utils import import_string
 
mail = Mail()
db = SQLAlchemy()
 
blueprints = [
    'myapp.main:main',
    'myapp.admin:admin',
]
 
def create_app(config):
    app = Flask(__name__)
    app.config.from_object(config)
 
    # Load extensions
    mail.init_app(app)
    db.init_app(app)
 
    # Load blueprints
    for bp_name in blueprints:
        bp = import_string(bp_name)
        app.register_blueprint(bp)
 
    return app
```
```
app = create_app('config')
app.run(host='0.0.0.0', debug=True)
```

我们不在代码中直接创建应用，而是通过调用”create_app()”方法来返回一个应用对象，这个”create_app()”就是应用工厂方法。在工厂方法里，我们分别加载了配置，扩展和蓝图。

### 使用消息队列



1. 可参考[timlardner/Docker-FlaskCeleryRabbitRedis](https://github.com/timlardner/Docker-FlaskCeleryRabbitRedis);
2. 参考[Web开发之旅--Flask使用Celery执行异步任务](https://mp.weixin.qq.com/s?__biz=MzI1ODAzMDY2NA==&mid=2247484446&idx=1&sn=b91ad8e81d9ebc1f75995e124cc806b6&chksm=ea0f2939dd78a02f042b922106d5860c4efc77ba1c8600cae9b41c9053824281e040a178fd9d&mpshare=1&scene=1&srcid=0311sjqtEyYUGzwWa7ViaKWU&pass_ticket=loo%2BacnyDhsVY9kWu3hzpVE2ZWjgCBeKfG8rma19zY8Kr7C%2FtjID7vxlCyBVKSnA#rd)
3. 问题一[Airflow: TypeError can't pickle memoryview objects](https://www.cnblogs.com/cord/p/9263491.html)
4. [http://www.pythondoc.com/flask-celery/index.html](http://www.pythondoc.com/flask-celery/index.html)

#### celery

1. 使用task嵌套时, 子task调用间隔不能太小，尤其在子task间结果相互依赖，特别是都操作数据库。
2. celery容器和APP容器分开运行，[参考](https://github.com/nickjj/build-a-saas-app-with-flask)。
```
celery
----status
--------AsyncResult    方法，获取任务ID的状态
```

### *使用[cookiecutter](https://github.com/cookiecutter-flask/cookiecutter-flask)*
```
myflaskapp
|---autoapp.py（调用create_app）
|---myflaskapp
    |---app.py(创建create_app)
    |---database.py(数据库)
    |---extensions.py(flask扩展集合)
    |---settings.py(配置)
    |---models.py（自己觉得可以把公用的orm放在这）
    |---public（其他业务）
        |---forms.py
        |---models.py
        |---views.py
        |---api.py(创建新的blueprint)
    |---user（用户管理）
        |---forms.py
        |---models.py（ORM）
        |---views.py(blueprint)
        |---api.py（创建新的blueprint）
    |---templates
        |---public(专有的页面)
        |---user（专有的页面）

```

### 使用[docker](https://github.com/tiangolo/uwsgi-nginx-flask-docker)

## 功能实现

### 认证
==（注：站点认证使用flask-login，API认证使用flask-httpauth）==
####  在 ==Flask-Login== 中，如果你不特殊处理的话，session 是在你关闭浏览器之后就失效的。
```
"自己控制 session 的过期时间"
session.permanent = True    "首先需要设置 login_manager 的 session类型为永久的"
app.permanent_session_lifetime = timedelta(minutes=5)    "然后再设置 session 的过期时间"
"同时，还需要注意的是 cookie 的默认有效期其实是 一年 的，所以，我们最好也设置一下"
login_manager.remember_cookie_duration=timedelta(days=1)
```

### [Flask：Flask-Script扩展与Click](http://www.harmel.cn/2018/08/flask-script.html)


## 插件

### 用户相关（登录、权限、角色）

#### [Flask-Principal](https://pythonhosted.org/Flask-Principal/)

1. [基于Flask-Principal的权限控制](https://www.jianshu.com/p/a2d4cf7cc9e2)

#### [Flask-login]

#### [Flask-httpauth]

#### [Flask-caching](https://github.com/sh4nks/flask-caching)

Flask-cache（已停止更新）的fork版。缓存支持。
参考[flask插件系列之flask_caching缓存](https://www.cnblogs.com/cwp-bg/p/9687005.html)

##### 常用参数配置
```
CACHE_TYPE:设置缓存的类型

# 下面五个参数是所有的类型共有的
CACHE_NO_NULL_WARNING = "warning" # null类型时的警告消息
CACHE_ARGS = [] # 在缓存类实例化过程中解包和传递的可选列表，用来配置相关后端的额外的参数
CACHE_OPTIONS = {}  # 可选字典,在缓存类实例化期间传递，也是用来配置相关后端的额外的键值对参数
CACHE_DEFAULT_TIMEOUT # 默认过期/超时时间，单位为秒
CACHE_THRESHOLD # 缓存的最大条目数

CACHE_TYPE = null # 默认的缓存类型，无缓存
CACHE_TYPE = 'simple' # 使用本地python字典进行存储，线程非安全

CACHE_TYPE = 'filesystem' # 使用文件系统来存储缓存的值
CACHE_DIR = "" # 文件目录

CACHE_TYPE = 'memcached' # 使用memcached服务器缓存
CACHE_KEY_PREFIX # 设置cache_key的前缀
CAHCE_MEMCACHED_SERVERS # 服务器地址的列表或元组
CACHE_MEMCACHED_USERNAME # 用户名
CACHE_MEMCACHED_PASSWORD # 密码

CACHE_TYPE = 'uwsgi' # 使用uwsgi服务器作为缓存
CACHE_UWSGI_NAME # 要连接的uwsgi缓存实例的名称

CACHE_TYPE = 'redis' # 使用redis作为缓存
CACHE_KEY_PREFIX # 设置cache_key的前缀
CACHE_REDIS_HOST  # redis地址
CACHE_REDIS_PORT  # redis端口
CACHE_REDIS_PASSWORD # redis密码
CACHE_REDIS_DB # 使用哪个数据库
# 也可以一键配置
CACHE_REDIS_URL 连接到Redis服务器的URL。示例redis://user:password@localhost:6379/2
```

##### 
