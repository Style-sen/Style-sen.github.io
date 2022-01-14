# PYTHON-语法


## 1. 运算符

### 1.1. 算术运算符

### 1.2. 赋值运算符

1. `+=`与`+`的区别。

```python
In [21]: a=[]
In [22]: a
Out[22]: []
In [23]: a+=b'1'
In [24]: a
Out[24]: [49]
In [25]: a= [] + b"2432"
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-25-5f6065d809aa> in <module>
----> 1 a= [] + b"2432"
TypeError: can only concatenate list (not "bytes") to list
```

### 1.3. 比较运算符

### 1.4. 逻辑运算符

### 1.5. 身份运算符

### 1.6. 成员运算符

### 1.7. 位运算符

## 2. 作用域

## 3. 循环

### 3.1. for

#### 3.1.1. 推导式（迭代器）

1. 列表推导式；
2. 字典推导式。

## 4. 异常捕获（可以弹出用户友好的弹窗）

```python
try:
    <监控异常>
except Exception[,reason]: # 捕获多个异常可以使用元组；可以使用as
    <捕获异常>
finally:
    <无论异常发生与否都发生>
```

### 4.1. 异常列表

1. `ValueError`
2. `AttributeError`

### 4.2. 自定义异常类型

1. 使用`raise`可以发出异常。

### 4.3. with

## 5. 函数

### 5.1. 定义函数

1. 变长参数。def func(*para,**paras):
2. 参数有默认值的话，可以省略；没有默认值，不可省略。
3. 函数中使用【赋值语句】（=）都会创建一个新变量，如果全局有同名变量，他们是不同的。如果是给全局变量重新赋值或改变他的值，需要使用`global`关键字.【注】

### 5.2. 调用函数

1. 使用`关键字参数`。c=func(a=1,b=2)

### 5.3. 迭代器和生成器

1. 使用关键字`yield`.配合`next()`使用。
2. `for in`会自动调用`next()`方法。

```python
def frange(start, end ,step):
    x=start
    while x< end:
        yield x
        x += step
```

### 5.4. lambda表达式

1. 作用：简化，没有函数名的函数。
2. `lambda x,y: x+y`

### 5.5. 闭包

1. 外部函数的变量被内部函数引用。变量不会释放。

```python
def sum(a):
    def add(b):
       return a+b
    return add

sum2=sum(2)
sum2(1)
```

2. 实现计数器的功能，调用一次自动加1

```python
def counter(start=0):
    cnt = [start] # 如果使用整形，下边再使用的时候会没有定义
    def add_one():
        cnt[0]+=1
        return cnt[0]
    return add_one
```

3. 实现一些科学计算。`有若干变量的值不变，变的是其他一些参数`。调用一次外部函数，传入不变的值；然后多次调用闭包。
4. 可以使用lambda优化闭包，内部函数使用lambda。

### 5.6. 装饰器

1. 使用场景：如计算程序的运行时间。不用重复一些代码

```python
def timer(func):
    def wrapper():
        start_time = time.time()
        func()
        stop_time = time.time()
        printf(f"{stop_time-start_time}")
    
    return wrapper

@timer
def func():

```

2. 带参数的装饰器。

```python
def new_tips(argv):
    # 判断argv
    def tips(func):
        def wrapper([这里的参数定义和装饰的函数的参数定义相同]):
            ####
            func([])
            ####
        return wrapper
    return tips

@new_tips("argv") # 装饰器带参数
def new_func():

```

### 5.7. 上下文管理器

1. `with`用法。

## 6. 模块

1. `import [py文件]` 会执行一遍py文件中的代码。
2. `from [py文件] import []`
3. `as`别名
