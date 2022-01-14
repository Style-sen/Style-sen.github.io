# 后端-web框架-Django-任务队列


## 基本信息

1. ~~[celery/django-celery](https://github.com/celery/django-celery)老的celery对django的集成，已经不再更新，最新版本为3.2.0（2016年11月23日），不再使用。~~
2. Celery 4.0支持Django 1.8和更新版本，所以直接使用celery即可`pip install celery`。
3. 使用[celery/django-celery-results](https://github.com/celery/django-celery-results/releases)将任务结果保存进Django ORM或者Django Cache中，最新版本为1.0.4（2018年11月14日）。
4. 参考[celery文档关于django的描述](http://docs.celeryproject.org/en/latest/django/index.html)。
5. ~~celery-with-redis不再使用，已不再更新。~~
6. celery任务放在每个APP下的tasks.py模块中。
7. 参考[Django初步使用Celery](http://yshblog.com/blog/163);
8. 参考[Django Celery定时任务和时间设置](http://yshblog.com/blog/164)。
