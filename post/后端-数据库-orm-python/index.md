# 后端-ORM-PYTHON


## 数据库设计

1. 数据库中的字段要区分永久性还是递变性，永久性存为一张表，递变性的字段单独为一张表，但是会随时间变化。
2. 永久性的字段衍伸的字段，可以用时重新计算；但是递变性的字段衍伸的字段，需要当时计算好之后存进数据库中。
3. 根据UML中定义的对象进行Model的设计，一个对象可对应多个Model。

## SQLAlchemy

SQLAlchemy is the Python SQL toolkit and Object Relational Mapper that gives application developers the full power and flexibility of SQL.

### 配置项

| | |
|--|--|
|

### Session状态管理

|语句|功能|
|--|--|
|db.session.expire(product)|清除对象里缓存的数据|
|db.session.refresh(product)|不仅清除对象里缓存的数据，还会立刻触发一次数据库查询更新数据|
|db.session.expire_all()|清除session里所有对象的缓存|
|db.session.flush()|所有本地修改写入到数据库，但没有提交|
|db.session.commit()|不仅把所有本地修改写入到数据库，同时也提交了该事务|
|db.session.delete() | 删除一条记录，参数只能是一条记录|

### [SQL Statements and Expressions API](https://docs.sqlalchemy.org/en/13/core/expression_api.html)SQl语句和表达式API

### 错误



### flask-sqlalchemy

    尽量使用sqlalchemy，而不用flask-sqlalchemy.

```
db = SQLAlchemy(app.app, session_options={'autocommit': True})
```

#### [flask-sqlalchemy配置](http://www.pythondoc.com/flask-sqlalchemy/config.html)

|||
|--|--|
|SQLALCHEMY_POOL_RECYCLE|	自动回收连接的秒数。这对 MySQL 是必须的，默认 情况下 MySQL 会自动移除闲置 8 小时或者以上的连接。 需要注意地是如果使用 MySQL 的话， Flask-SQLAlchemy 会自动地设置这个值为 2 小时。|

```
@app.teardown_appcontext
def shutdown_session(exception=None):
    db.session.remove()
```

1. [run example InvalidRequestError: No transaction is begun. #47](https://github.com/mitsuhiko/flask-openid/issues/47)
2. [Invalid transaction persisting across requests](https://stackoverflow.com/questions/23301968/invalid-transaction-persisting-across-requests)
3. [Error 2006: MySQL server has gone away](https://serverfault.com/questions/407612/error-2006-mysql-server-has-gone-away)
4. [flask_sqlalchemy中db.session是如何保持请求间独立的--源码阅读笔记](https://www.cnblogs.com/lgjbky/p/9482278.html)
5. ["Mysql has gone away"的几种可能](https://www.cnblogs.com/lesliexong/p/8654615.html)
6. SQLAlchemy中的create engine默认会用到MySQLdb模块.但是此模块只支持python2.7.所以需要改变与mysql的连接模块(例如pymysql).
```
engine = create_engine("mysql+pymysql://root:xxxx@192.168.1.249:9306/TestDataBank")
```
7. [Flask 将数据库表转换为sqlalchemy models](https://blog.csdn.net/powerccna/article/details/79315156)  
8. [flask sqlalchemy 如何在models定义两个不同数据库，表名相同（__tablename__）的class](https://segmentfault.com/q/1010000007044608)

#### 使用

##### 对象
```python
sqlalchemy.create_engine  # 创建一个数据库引
sqlalchemy.Column  # 纵队
sqlalchemy.Table  # 表格
    Table.columns  # 等同于 .c
    Table.insert()  # 获得一个插入句柄
        insert().execute(name='rsj217', email='rsj21@gmail.com')  # 插入一条数据
        insert().execute({字典},{字典})  # 插入多条数据
sqlalchemy.Index
sqlalchemy.text
sqlalchemy.PassiveDefault  # 定义某一列的默认操作
sqlalchemy.Numeric
sqlalchemy.Integer  # 整数
sqlalchemy.String  # 字符串
sqlalchemy.text
sqlalchemy.DateTime
sqlalchemy.MetaData  # 元数据,容器类对象
sqlalchemy.exc.OperationalError
sqlalchemy.schema.CreateTable
sqlalchemy.select
sqlalchemy.dialects
    dialects.mysql
        mysql.FLOAT  # mysql的float类型 precision设置有效数字膈腧scale设置小数点后数字个数
        mysql.DOUBLE
        mysql.REAL  # 可以保留原数据的精度
        mysql.TIMESTAMP  # 时间戳
```
##### 常用的SQLAlchemy查询过滤器（query.）
    
过滤器 |	说明
--|--
filter()|	把过滤器添加到原查询上，返回一个新查询
filter_by()|	把等值过滤器添加到原查询上，返回一个新查询
limit|	使用指定的值限定原查询返回的结果
offset()|	偏移原查询返回的结果，返回一个新查询
order_by()|	根据指定条件对原查询结果进行排序，返回一个新查询
group_by()	|根据指定条件对原查询结果进行分组，返回一个新查询

```python
User.query.filter(User.name.endswith('g')).all()
User.query.filter(User.name!='wang').all()
User.query.filter(and_(User.name!='wang',User.email.endswith('163.com'))).all()
User.query.filter(or_(User.name!='wang',User.email.endswith('163.com'))).all()
User.query.filter(not_(User.name=='chen')).all()
User.query.filter_by(name='zhang').update({'name':'li'})
# 查询名字结尾字符为g的所有用户[开始 / 包含]
User.query.filter(User.name.endswith("g")).all()
User.query.filter(User.name.startswith("w")).all()
User.query.filter(User.name.contains("n")).all()
User.query.filter(User.name.like("%n%g")).all()  模糊查询
# 查询名字和邮箱都以li开头的所有用户[2种方式]
User.query.filter(User.name.startswith("li"), User.email.startswith("li")).all()
from sqlalchemy import and_
User.query.filter(and_(User.name.startswith("li"), User.email.startswith("li"))).all()
# 查询age是25 或者 `email`以`itheima.com`结尾的所有用户
from sqlalchemy import or_
User.query.filter(or_(User.age == 25, User.email.endswith("itheima.com"))).all()
# 查询名字不等于wang的所有用户[2种方式]
from sqlalchemy import not_
User.query.filter(not_(User.name == "wang")).all()
User.query.filter(User.name != "wang").all()
# 查询id为[1, 3, 5, 7, 9]的用户
User.query.filter(User.id.in_([1, 3, 5, 7, 9])).all()
# 所有用户先按年龄从小到大, 再按id从大到小排序, 取前5个
User.query.order_by(User.age, User.id.desc()).limit(5).all()
```

##### 常用的SQLAlchemy查询执行器
    
方法|	说明
--|--
all()|	以列表形式返回查询的所有结果
first()|	返回查询的第一个结果，如果未查到，返回None
first_or_404()|	返回查询的第一个结果，如果未查到，返回404
get()|	返回指定主键对应的行，如不存在，返回None
get_or_404()|	返回指定主键对应的行，如不存在，返回404
count()	|返回查询结果的数量
paginate()|	返回一个Paginate对象，它包含指定范围内的结果
    
```python
    
    
    "数据库分页"
    [employees] = [Employee].query.paginate([page],per_page=[10])            "paginate是分页的方法，第一个参数是页码，第二个是每页显示多少条"
    "属性"
    [employees].items    "得到的结果不是一列表,需要在传到前台的值加一个 .items"
    [employees].pages    "总的页数"
    [employees].page     "当前页"
    paginate.prev_num    "上一页页码"
    paginate.next_num    "下一页页码"
    paginate.has_prev    "有无上一页 True/False"
    paginate.has_next    "有无下一页 True/False"
    paginate.pages       "查询总页数"
    paginate.total       "记录总数"
    paginate.per_page    "每页记录数量"
    "方法"
    .prev() 上一页的分页对象Pagination
    .next() 下一页的分页对象Pagination
    paginate.iter_pages()"方法  分页导航列表"
    "用来获得针对当前页的应显示的分页页码列表"
    "假设当前共有100页，当前页为50页，按照默认的参数设置调用iter_pages获得的列表为"
    "[1,2,None,48,49,50,51,52,53,54,55,None,99,100]"

    # paginate,对象，包含当前页的数据，paginate.items可被for循环遍历,每个遍历值为一个模型对象

    [pag] = [News].query.filter().order_by(News.create_time.desc()).paginate([page], [per_page], [False])
```

2. Automap 也可以使用automap.
3. Flask中使用SqlAlchemy的较好方法:使用脚本生成models,其中用到了sqlautocode模块,此模块已经过时,已经迁移到更新的模块:flask-sqlacodegen.
```
flask-sqlacodegen mysql+pymysql://root:xxx@xxx.xxx.xxx/TestDataBank
```
（注意：此处加上--flask可以生成flask-SQLAlchemy对应的model。）
可以生成相应的table类文件.此种方法有一个弊端就是,如果更改了数据库的内容,还需要重新生成model文件.此时,就需要先更改model,然后使用数据库迁移命令增量更新数据库.
4. 迁移：flask-migrate 管理升级迁移数据库         Flask-Migrate  
5. 

## Django ORM（Models）

### 1. Manager
### 2. QuerySet
1. Manager类的一些方法会返回QuerySet实例，QuerySet是一个可遍历结构，包含一个或多个元素==每个元素都是一个Model 实例(get =获得一个实例)==，它里面的方法也是表级方法.
. QuerySet类似于Python中的list，list的一些方法QuerySet也有，比如切片，遍历。
3. QuerySet是延迟获取的，只有当用到这个QuerySet时，才会查询数据库求值。另外，查询到的QuerySet又是缓存的，当再次使用同一个QuerySet时，并不会再查询数据库，而是直接从缓存获取（不过，有一些特殊情况）。一般而言，当对一个没有求值的QuerySet进行的运算，返回的是QuerySet、ValuesQuerySet、ValuesListQuerySet、Model实例时，一般不会立即查询数据库；反之，当返回的不是这些类型时，会查询数据库。
4. ==from django.forms.models import model_to_dict 转换成字典对象==
5. 下面介绍几种（并非全部）对QuerySet求值的场景。

### Field Options（Feild选项）

1. 所有的参数对所有的field都有用，而且都是可选的。

#### null

1. 若为True，则可以存储空值NULL。默认为False

#### blank

1. 若为True,则可以为空。默认为False

#### default

1. 默认值。可以是一个值也可以是一个方法。不能是可变对象。

### Field类型

|Field类型|参数|功能|
|--|--|--|
|AutoField           ||"如果没有指明主键，就会产生一个自增的主键"|
|BigIntegerField     ||"64位的整型数值，从 -2^63 (-9223372036854775808) 到 2^63-1(9223372036854775807)"|
|BinaryField         ||"存储原始二进制数据，仅支持字节分配。功能有限"|
|BooleanField        ||"布尔型和NullBooleanField有区别，true/false，本类型不允许出现null"
|CharField           ||"字符串，一般都在创建时写入max_length参数""注意：使用django rest framework此字段必须要指定max_length参数"|
|DateField           |`auto_now`表示每次修改时改变时间`auto_now_add`表示创建时表示时间|时间，对应Python的datetime.date，一般来说数据库重要的表都要有这样的字段记录创建字段时间个最后一次改变的时间。关于时间的话，建议timestamp，当然 python的话还是DateTime吧"
|DateTimeField       |同上|对应Python的datetime.datetime
|DecimalField        ||"固定精度的十进制数，一般用来存金额相关的数据。对应python的Decimal，额外的参数包括DecimalField.max_digits和DecimalField.decimal_places，这个还是要参照一下mysql的Decimal类型.例如：price = models.DecimalField(max_digits=8,decimal_places=2)"
|EmailField          ||字符串，会检查是否是合法的email地址|
|FileField           ||class FileField([upload_to=None, max_length=100, **options])存文件的，参数upload_to在1.7之前的一些老版本中必选的
|FloatField          ||浮点数，必填参数：max_digits，数字长度；decimal_places，有效位数。
|ImageField          ||class ImageField([upload_to=None, height_field=None, width_field=None, max_length=100, **options])图片文件类型，继承了FileField的所有属性和方法。参数除upload_to外，还有height_field，width_field等属性。
|IntegerField        ||[-2147483648,2147483647 ]的取值范围对Django所支持的数据库都是安全的。
|IPAddressField      ||点分十进制表示的IP地址，如10.0.0.1
|GenericIPAddressField||ip v4和ip v6地址表示，ipv6遵循RFC 4291section 2.2,
|NullBooleanField    ||可以包含空值的布尔类型，相当于设置了null=True的BooleanField。
|PositiveIntegerField||正整数或0类型，取值范围为[0 ,2147483647]
|PositiveSmallIntegerField||正短整数或0类型，类似于PositiveIntegerField，取值范围依赖于数据库特性，[0 ,32767]的取值范围对Django所支持的数据库都是安全的。
|SlugField           ||只能包含字母，数字，下划线和连字符的字符串，通常被用于URLs表示。可选参数max_length=50，prepopulate_from用于指示在admin表单中的可选值。db_index，默认为True。
|SmallIntegerField   ||小整数字段，类似于IntegerField，取值范围依赖于数据库特性，[-32768 ,32767]的取值范围对Django所支持的数据库都是安全的。
|TextField           ||"文本类型"
|TimeField           ||时间，对应Python的datetime.time
|URLField            ||存储URL的字符串，默认长度200；verify_exists(True)，检查URL可用性。
|FilePathField       ||class FilePathField(path=None[, match=None, recursive=False, max_length=100, **options])类似于CharField，但是取值被限制为指定路径内的文件名，path参数是必选的。
```
"常见参数"
null        "如果设置为 True , Django 存放一个 NULL 到数据库字段。默认为 False。"
blank       "如果设置为 True , 此 field 允许为 blank （空白），默认为 False。"
choices     "一个2元元组的元组或者列表，如果执行 choices ， Django 的 admin 就会使用 选择框而不是标准的 text 框填写这个 field"
YEAR_IN_SCHOOL_CHOICES = ((u'FR', u'Freshman'),(u'SO', u'Sophomore'),(u'JR', u'Junior'),(u'SR', u'Senior'),(u'GR', u'Graduate'),)
"2元元组的第一个元素是要存入 database 的数据，第二个元素是 admin 的界面 显示的数据。"
使用了 choices 参数的 field 在其 model 示例里，可以用 "get_field的名 字_display" 方法 显示 choices 的显示字串（就是2元元组的第二个数据）。示 例：
from django.db import models
class Person(models.Model):
    GENDER_CHOICES = (
        (u'M', u'Male'),
        (u'F', u'Female'),
    )
    name = models.CharField(max_length=60)
    gender = models.CharField(max_length=2, choices=GENDER_CHOICES)
>>> p = Person(name="Fred Flinstone", gender="M")
>>> p.save()
>>> p.gender
u'M'
>>> p.get_gender_display()
u'Male'
default    "field 的默认值，可以使用可调用对象（a callable object），如果使用可调用 对象，那么每次创建此 model 的新对象时调用可调用对象。常见如 datatime "
help_text  "help_text 的值可以在 admin form 里显示，不过即使不使用 admin ，也可以当 做描述文档使用"
primary_key
如果为 True ， 这个 field 就是此 model 的 primary key 。
unique
如果为 True， 此 field 在这个 table 里必须唯一。
verbose_name
verbose，详细的意思。verbose_name，就可以理解为详细的名字吧。
除了ForeignKey, ManyToManyField 和 OneToOneField之外，每个类型的字段都有一个可选的第一位置参数－详细的名字。如果没有给出详细的名称，Django将自动使用字段的属性名来代替他。替代过程中会转换下划线为空格。

该字段中，名字的详情为”person’s first name”：

first_name = models.CharField("person's first name", max_length=30)

以下字段中，first_name的详细名字为"first name":

first_name = models.CharField(max_length=30)

ForeignKey, ManyToManyField 和 OneToOneField要求第一个参数是模型的类，所以需要使用verbose_name关键字参数，如：

poll = models.ForeignKey(Poll, verbose_name="the related poll")

sites = models.ManyToManyField(Site, verbose_name="list of sites")

place = models.OneToOneField(Place, verbose_name="related place")

在需要的时候Django会自动大写 verbose_name的首字母。

原来verbose_name字段就是为ForeignKey, ManyToManyField 和 OneToOneField这三种关系准备的啊！

```

### 4. 常用操作:
```python
"""
1. values
my_string = "id", "name"
my_model.objects.values(*my_string)
"""
"__gt  大于    __gte  大于等于"
User.objects.filter(age__gt=10)    // 查询年龄大于10岁的用户
User.objects.filter(age__gte=10)  // 查询年龄大于等于10岁的用户
"__lt  小于    __lte 小于等于"
User.objects.filter(age__lt=10)     // 查询年龄小于10岁的用户
User.objects.filter(age__lte=10)   // 查询年龄小于等于10岁的用户
"__in"
查询年龄在某一范围的用户
User.objects.filter(age__in=[10, 20, 30])
"like"
__exact        精确等于 like 'aaa'
__iexact       精确等于 忽略大小写 ilike 'aaa'
__contains     包含 like '%aaa%'
__icontains    包含 忽略大小写 ilike '%aaa%'，但是对于sqlite来说，contains的作用效果等同于icontains。
"__isnull  判空"
User.objects.filter(username__isnull=True)    // 查询用户名为空的用户
User.objects.filter(username__isnull=False)  // 查询用户名不为空的用户
"不等于/不包含于"
User.objects.filter().excute(age=10)    // 查询年龄不为10的用户
User.objects.filter().excute(age__in=[10, 20])  // 查询年龄不为在 [10, 20] 的用户
"range"
Tb1.objects.filter(id__range=[1, 2])   # 范围bettwen and
"startswith，istartswith, endswith, iendswith"
"order by"
Tb1.objects.filter(name='seven').order_by('id')    # asc
Tb1.objects.filter(name='seven').order_by('-id')   # desc
"group by"
from django.db.models import Count, Min, Max, Sum
models.Tb1.objects.filter(c1=1).values('id').annotate(c=Count('num'))
如同:SELECT "app01_tb1"."id", COUNT("app01_tb1"."num") AS "c" FROM "app01_tb1" WHERE "app01_tb1"."c1" = 1 GROUP BY "app01_tb1"."id"
```
==django do not support group_concat==
3functions:
1. raw sql.
2. django-mysql
3. [Creating your own Aggregate Functions](https://docs.djangoproject.com/en/dev/ref/models/expressions/#django.db.models.Aggregate)
```
"limit 、offset"
models.Tb1.objects.all()[10:20]
#### 2.12 regex正则匹配，iregex 不区分大小写
Entry.objects.get(title__regex=r'^(An?|The) +')
Entry.objects.get(title__iregex=r'^(an?|the) +')
#### 2.13 date
Entry.objects.filter(pub_date__date=datetime.date(2005, 1, 1))
Entry.objects.filter(pub_date__date__gt=datetime.date(2005, 1, 1))
#### 2.14 year  month  day 类似
Entry.objects.filter(pub_date__year=2005)
Entry.objects.filter(pub_date__year__gte=2005)
#### 2.15 week_day
Entry.objects.filter(pub_date__week_day=2)
Entry.objects.filter(pub_date__week_day__gte=2)
#### 2.16 hour  minute second类似
Event.objects.filter(timestamp__hour=23)
Event.objects.filter(time__hour=5)
Event.objects.filter(timestamp__hour__gte=12)
#### 2.17 filter是包含,exclude是不包含
#### 2.18 orm的F功能    就是用来更新获取原来值的功能
假设数据库有一个员工表，表中的年龄都自加“1”，这里就需要到orm的F功能，如下面的代码：
from django.db.models import F#首先要导入这个F模块
models.Uinfo.objects.all().update(age=F("age")+1)#这里的F功能后面的age，它就会让数据表表中的age这列+1
#### 2.19 orm的Q功能  Q能把查询相互嵌套    Q用于构造复杂的查询条件的，使用方法有对象方法和直接创建创建对象方法
数据库的查询条件我们可以使用filter，在filter里面的可以是两个条件他们之间是and的关系，也可以是一个字典，例如下面的代码
models.Uinfo.objects.all().filter(id=1,name=‘李伟‘)
conditon={‘id‘:‘1‘,‘name‘:‘李伟‘}
models.Uinfo.objects.all().filter(**conditon)
除了上面的方法，我们还可以加Q的对象，例如：
from django.db.models import Q
models.Uinfo.objects.all().filter(Q(id=1))#条件是id为1的时候
models.Uinfo.objects.all().filter(Q(id=1)|Q(id__gt=3))#条件是或的关系，|
models.Uinfo.objects.all().filter(Q(id=1) & Q(id=4))# 条件是and的关系
Q的另外一种用法
#q1 里面的条件都是or的关系
    q1=Q()
    q1.connector = ‘OR‘
    q1.children.append((‘id‘,1))
    q1.children.append((‘id‘,3))
    q1.children.append((‘id‘,6))
#q2里面的条件都是or的关系
    q2=Q()
    q2.connector = ‘OR‘
    q2.children.append((‘c‘,2))
    q2.children.append((‘c‘,4))
    q2.children.append((‘c‘,6))
#con 通过and的条件把q1和q2 联系到一块 
    con=Q()
    con.add(q1,‘AND‘)
    con.add(q2,‘AND‘)
#### 2.20 extra 在QuerySet的基础上继续执行子语句 extra(self, select=None, where=None, params=None, tables=None, order_by=None, select_params=None) 
models.UserInfo.objects.extra(
                    select={'newid':'select count(1) from app01_usertype where id>%s'},
                    select_params=[1,],
                    where = ['age>%s'],
                    params=[18,],
                    order_by=['-age'],
                    tables=['app01_usertype']
                )
                """
                select 
                    app01_userinfo.id,
                    (select count(1) from app01_usertype where id>1) as newid
                from app01_userinfo,app01_usertype
                where 
                    app01_userinfo.age > 18
                order by 
                    app01_userinfo.age desc
                """
#### 2.21 执行原生SQL
* 更高灵活度的方式执行原生SQL语句
```python
from django.db import connection, connections
cursor = connection.cursor()  # cursor = connections['default'].cursor()
cursor.execute("""SELECT * from auth_user where id = %s""", [1])
row = cursor.fetchone()
```
#### 2.22 去重
models.Book.objects.values("title").distinct()
【8】优化
[8.1]select_related实现表之间进行一对一和多对一优化
def select_related(self, *fields)
    性能相关：表之间进行join连表操作，一次性获取关联的数据。
    总结：
    1. select_related主要针一对一和多对一关系进行优化。
    2. select_related使用SQL的JOIN语句进行优化，通过减少SQL查询的次数来进行优化、提高性能。
实例代码如下：a1 = models.Book.objects.all()
for i in a1:print(i.publisher.name)    沙河出版社    沙河出版社  三里屯  五道口  国贸  五道口
a2 = models.Book.objects.all().select_related("publisher")
for i in a2:print(i.publisher.name)  沙河出版社  沙河出版社  五道口  五道口  三里屯  国贸
以上代码中第一种查询方式会查询数据库六次，第二种方式会查询数据库次数为1次
[8.2]prefetch_related实现表之间进行一对多和多对多优化     只能正向查询~且不能跨表
def prefetch_related(self, *lookups)
    性能相关：多表连表操作时速度会慢，使用其执行多次SQL查询在Python代码中实现连表操作。
总结：
    1. 对于多对多字段（ManyToManyField）和一对多字段，可以使用prefetch_related()来进行优化。
    2. prefetch_related()的优化方式是分别查询每个表，然后用Python处理他们之间的关系。
tmp = models.Author.objects.all().prefetch_related("books")
for i in tmp:
    print(i.books.all())
<QuerySet [<Book: 书一>, <Book: 书一>]>
<QuerySet [<Book: 书一>, <Book: 书三(第)>]>
<QuerySet [<Book: 书一>]>
<QuerySet []>
[8.3]defer 映射中排除某列数据   [在查询中排除name字段，但是在循环中如果要输出关于name的信息，那么依然会继续查询输出name]
[8.4]only 仅取某个表中的数据
[8.5]raw执行原生SQL   执行原生SQL     tmp = models.Author.objects.raw("select * from app05_author")
[8.6]为原生SQL设置参数  a = models.Book.objects.raw("select id from app05_book WHERE id>%s",params=[3,])
[8.7]dates
def dates(self, field_name, kind, order='ASC'):
    # 根据时间进行某一部分进行去重查找并截取指定内容
    # kind只能是："year"（年）, "month"（年-月）, "day"（年-月-日）
    # order只能是："ASC"  "DESC"
    # 并获取转换后的时间
        - year : 年-01-01
        - month: 年-月-01
        - day  : 年-月-日
示例代码如下： 

models.Book.objects.all().dates("publish_day","year","DESC")
<QuerySet [datetime.date(2018, 1, 1), datetime.date(2017, 1, 1)]>
models.Book.objects.all().dates("publish_day","month","DESC")
<QuerySet [datetime.date(2018, 4, 1), datetime.date(2018, 1, 1), datetime.date(2017, 1, 1)]>
models.Book.objects.all().dates("publish_day","day","DESC")
<QuerySet [datetime.date(2018, 4, 12), datetime.date(2018, 1, 29), datetime.date(2018, 1, 25), datetime.date(2018, 1, 19), datetime.date(2018, 1, 11), datetime.date(2017, 1, 18)]>
[8.8]datetimes
def datetimes(self, field_name, kind, order='ASC', tzinfo=None):
    # 根据时间进行某一部分进行去重查找并截取指定内容，将时间转换为指定时区时间
    # kind只能是 "year", "month", "day", "hour", "minute", "second"
    # order只能是："ASC"  "DESC"
    # tzinfo时区对象
    models.DDD.objects.datetimes('ctime','hour',tzinfo=pytz.UTC)
    models.DDD.objects.datetimes('ctime','hour',tzinfo=pytz.timezone('Asia/Shanghai'))
    """
    pip3 install pytz
    import pytz
    pytz.all_timezones
    pytz.timezone(‘Asia/Shanghai’)
    """
[8.9]bulk_create 
参数为一次性插入的个数
objs = [models.Publisher(name="aaa",addr="aaa"),models.Publisher(name="bbb",addr="bbb")]
models.Publisher.objects.bulk_create(objs,2)
[<Publisher: aaa>, <Publisher: bbb>]
[8.10]get_or_create 
# 如果存在，则获取，否则，创建 
# defaults 指定创建时，其他字段的值
models.Publisher.objects.get_or_create(name="沙河出版社")
(<Publisher: 沙河出版社>, False)
models.Publisher.objects.get_or_create(name="沙河出版社1")
(<Publisher: 沙河出版社1>, True)
models.Publisher.objects.get_or_create(name="沙河出版1",addr="sss")
(<Publisher: 沙河出版1>, True)
models.Publisher.objects.get_or_create(name="沙河版1",defaults={"addr":"sdfsdsf"})
(<Publisher: 沙河版1>, True)

[8.11]update_or_create 
如果存在，则更新，否则，创建 
defaults 指定创建时或更新时的其他字段
obj, created = models.UserInfo.objects.update_or_create(username='root1', defaults={'email': '1111111','u_id': 2, 't_id': 1})

[8.12]in_bulk 
# 根据主键ID进行查找 
id_list = [11,21,31] 
models.DDD.objects.in_bulk(id_list)
from app05 import models
models.Book.objects.in_bulk([1,2,3])
(0.000) SELECT @@SQL_AUTO_IS_NULL; args=None
(0.001) SELECT VERSION(); args=None
(0.002) SELECT `app05_book`.`id`, `app05_book`.`title`, `app05_book`.`price`, `app05_book`.`publish_day`, `app05_book`.`publisher_id` FROM `app05_book` WHERE `app05_book`.`id` IN (1, 2, 3); args=(1, 2, 3)
{1: <Book: 书一>, 2: <Book: 书一>, 3: <Book: 书三(第)>}

