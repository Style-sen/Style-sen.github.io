# python-第三方库


## 1. [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)是一个可以从HTML或XML文件中提取数据的Python库

## 2. [click](https://click.palletsprojects.com/en/7.x/)

参考[Python命令行神器 Click 简明笔记](https://blog.csdn.net/lihua_tan/article/details/54869355)

1. 使用 @click.command()装饰一个函数，使之成为命令行接口；
2. 使用 @click.option()等装饰函数，为其添加命令行选项等。

    ```python
    1. 第一个参数指定了命令行选项的名称，如`--count`
    2. option 常用的设置参数如下：
        default: 设置命令行参数的默认值
        help: 参数说明
        type: 参数类型，可以是 string, int, float 等；也可以通过 click.Choice([ARRAY]) 来限定可选值
        prompt: 当在命令行中没有输入相应的参数时，会根据 prompt 提示用户输入
        nargs: 指定此参数接收的值的个数
    3. option 提供了两个参数来设置密码的输入：hide_input 和 confirmation_promt，其中，hide_input=true 用于隐藏输入，    confirmation_promt=true 用于重复输入；由于上面的写法有点繁琐，click 也提供了一种快捷的方式，通过使用@click.password_option(),增加参数password
    4. 改变命令行程序的执行
        is_eager=True 表明该命令行选项优先级高于其他选项；
        expose_value=False 表示如果没有输入该命令行选项，会执行既定的命令行流程；
        callback 指定了输入该命令行选项时，要跳转执行的函数；

    ```

3. 代码中使用 click.echo 进行输出是为了获得更好的兼容性，因为 print在 Python2 和 Python3 的用法有些差别。配合 colorama 这个模块，我们可以使用 click.secho 进行彩色输出。

    ```python
    fg 表示前景颜色（即字体颜色），可选值有：BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE 等；
    bg 表示背景颜色，可选值有：BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE 等；
    underline 表示下划线，可选的样式还有：dim=True，bold=True等；
    ```

4. 除了使用 @click.option 来添加可选参数，还会经常使用@click.argument("参数名称") 来添加固定参数。它的使用和 option 类似，但支持的功能比 option 少。

    ```python
    argument 可以接收不定量的参数。nargs=-1 表明参数 src 接收不定量的参数值，参数值会以 tuple 的形式传入函数。如果 nargs 大于等于 1，表示接收nargs个参数值
    ```

## 3. [Matplotlib]()

## 4. [numpy]() 数据预处理

1. 用于【高性能】科学计算和数据分析。
2. NumPy 最重要的一个特点是其 【N 维数组对象 ndarray】，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。
3. ndarray 中的每个元素在内存中都有相同存储大小的区域。
4. 创建一个 ndarray 只需调用 NumPy 的 array 函数即可。
5. [numpy 支持的数据类型比 Python 内置的类型要多很多，基本上可以和 C 语言的数据类型对应上，其中部分类型对应为 Python 内置的类型](https://www.runoob.com/numpy/numpy-dtype.html)

### 4.1. def 方法

#### 4.1.1. `numpy.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)`

#### 4.1.2. `numpy.zeros()`

#### 4.1.3. `numpy.ones()`

#### 4.1.4. `numpy.empty()`

#### 4.1.5. `numpy.arange()`

### 4.2. ndarray方法

1. `copy()`

## 5. [pywifi]

## 6. [psycopg2]()

操作PostgreSQL 数据库

## 7. [requests](http://docs.python-requests.org/zh_CN/latest/)

```shell
>>> payload = {'key1': 'value1', 'key2': 'value2'}
>>> r = requests.get("http://httpbin.org/get", params=payload)
返回的r是一个reponse对象
r.url    目标的url
r.json()    使用json解码
r.encoding    text的编码。可以设置
r.text    响应的内容
r.content    以字节的方式访问请求响应体
r.status_code    请求代码
r.headers    响应的头
```

### 7.1. def 方法

#### 7.1.1. get

1. `def get(url, params=None, **kwargs)`

### 7.2. class 类

#### 7.2.1. Session

## 8. seaborn

1. Seaborn is a Python data visualization library 【based on matplotlib】.

