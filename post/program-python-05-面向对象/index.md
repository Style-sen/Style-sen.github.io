# Python-面向对象


## 1. 面向对象 [参考](https://www.liaoxuefeng.com/wiki/1016959663602400/1017496679217440)

1. 面向对象编程——Object Oriented Programming，简称OOP，是一种程序设计思想。OOP把对象作为程序的基本单元，一个对象包含了数据和操作数据的函数。
2. 面向对象的程序设计把计算机程序视为一组对象的集合，而每个对象都可以接收其他对象发过来的消息，并处理这些消息，计算机程序的执行就是一系列消息在各个对象之间传递。
3. **注:对ORM中的model就可以看做一个对象，可以定义丰富的方法。**

### 1.1. 类和实例

1. 面向对象最重要的概念就是类（Class）和实例（Instance），类是抽象的模板，而实例是根据类创建出来的一个个具体的“对象”，每个对象都拥有相同的方法，但各自的数据可能不同。

#### 1.1.1. 类属性

直接在class中定义属性，这种属性是类属性，这个属性虽然归类所有，但类的所有实例都可以访问到，但是不能修改，只能类名.属性进行修改。

#### 1.1.2. 实例属性

1. 在类方法中定义的属性为实例属性，各个实例相互独立。
2. 如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。
3. 通过方法访问私有变量，可以对参数做检查，避免传入无效的参数。
4. 变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。
5. 有些时候，你会看到以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。
6. 双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问__name是因为Python解释器对外把__name变量改成了_Student__name，所以，仍然可以通过_Student__name来访问__name变量。

#### 1.1.3. 动态绑定属性和方法

1. 正常情况下，当我们定义了一个class，创建了一个class的实例后，我们可以给该实例绑定任何属性和方法，这就是动态语言的灵活性。

```shell
>>> s = Student()
>>> s.name = 'Michael' # 动态给实例绑定一个属性
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
为了给所有实例都绑定方法，可以给class绑定方法：
>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score
```

2. 为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性：

```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

#### 1.1.4. 使用@property

Python内置的@property装饰器就是负责把一个方法变成属性调用的。

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```

@property的实现比较复杂，我们先考察如何使用。把一个getter方法变成属性，只需要加上@property就可以了，此时，@property本身又创建了另一个装饰器@score.setter，负责把一个setter方法变成属性赋值，于是，我们就拥有一个可控的属性操作.

#### 1.1.5. 定制类(特殊属性)

##### 1.1.5.1. `__call__`

* 对象可call，注意不是类，是对象
* 对象通过提供__call__(self, [,*args [,**kwargs]])方法可以模拟函数的行为，如果一个对象x提供了该方法，就可以像函数一样使用它，也就是说x(arg1, arg2...) 等同于调用x.__call__(self, arg1, arg2) 。模拟函数的对象可以用于创建防函数(functor) 或代理(proxy)

##### 1.1.5.2. `__dict__`

* 包含这个对象的所有属性;
* 不是所有的对象都有 __dict__ 属性。例如，如果你在一个类中添加了 __slots__ 属性，那么这个类的实例将不会拥有 __dict__ 属性，但是 dir() 仍然可以找到并列出它的实例所有有效属性。
* 同理许多内建类型都没有 __dict__ 属性，例如 list 没有 __dict__ 属性，但你仍然可以通过 dir() 列出 list 所有的属性。

##### 1.1.5.3. `__enter__`

##### 1.1.5.4. `__exit__`

1. 在__exit__中返回True,则不会产生异常.
2. 在__init__或者__enter__中抛出异常，则不会进入到__exit__中

##### 1.1.5.5. `__getattr__`

1. 只有在没有找到属性的情况下，才调用__getattr__，已有的属性，比如name，不会在__getattr__中查找。
2. 利用完全动态的__getattr__，我们可以写出一个链式调用：

```python
class Chain(object):

    def __init__(self, path=''):
        self._path = path

    def __getattr__(self, path):
        return Chain('%s/%s' % (self._path, path))

    def __str__(self):
        return self._path

    __repr__ = __str__
试试：

>>> Chain().status.user.timeline.list
'/status/user/timeline/list'
```

##### 1.1.5.6. `__getitem__`

1. Fib实例虽然能作用于for循环，看起来和list有点像，但是，把它当成list来使用还是不行，比如，取第5个元素：

```shell
>>> Fib()[5]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'Fib' object does not support indexing
```
要表现得像list那样按照下标取出元素，需要实现__getitem__()方法：
```
class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a
```
现在，就可以按下标访问数列的任意一项了：
```
>>> f = Fib()
>>> f[0]
1
>>> f[1]
1
>>> f[2]
2
>>> f[3]
3
>>> f[10]
89
>>> f[100]
573147844013817084101
```
但是list有个神奇的切片方法：
```
>>> list(range(100))[5:10]
[5, 6, 7, 8, 9]
```
对于Fib却报错。原因是__getitem__()传入的参数可能是一个int，也可能是一个切片对象slice，所以要做判断：
```
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, int): # n是索引
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        if isinstance(n, slice): # n是切片
            start = n.start
            stop = n.stop
            if start is None:
                start = 0
            a, b = 1, 1
            L = []
            for x in range(stop):
                if x >= start:
                    L.append(a)
                a, b = b, a + b
            return L
```
现在试试Fib的切片：
```
>>> f = Fib()
>>> f[0:5]
[1, 1, 2, 3, 5]
>>> f[:10]
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```
但是没有对step参数作处理：
```
>>> f[:10:2]
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```
也没有对负数作处理，所以，要正确实现一个__getitem__()还是有很多工作要做的。

此外，如果把对象看成dict，__getitem__()的参数也可能是一个可以作key的object，例如str。

与之对应的是__setitem__()方法，把对象视作list或dict来对集合赋值。最后，还有一个__delitem__()方法，用于删除某个元素。

总之，通过上面的方法，我们自己定义的类表现得和Python自带的list、tuple、dict没什么区别，这完全归功于动态语言的“鸭子类型”，不需要强制继承某个接口。


##### 1.1.5.7. `__init__`

* 对象的初始化， 是一个实例方法，第一个参数是self。

##### 1.1.5.8. `__iter__`

1. 如果一个类想被用于for ... in循环，类似list或tuple那样，就必须实现一个__iter__()方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的__next__()方法拿到循环的下一个值，直到遇到StopIteration错误时退出循环。
```
我们以斐波那契数列为例，写一个Fib类，可以作用于for循环：

class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1 # 初始化两个计数器a，b

    def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b # 计算下一个值
        if self.a > 100000: # 退出循环的条件
            raise StopIteration()
        return self.a # 返回下一个值
```

##### 1.1.5.9. `__new__`

* 对象的创建，是一个静态方法，第一个参数是cls;
* 与__init__一样，每当实例化类时MyClass(*args, **kwargs) ，__new__ 都会默认自动执行MyClass.__new__(*args, **kwargs)，如果想改变默认__new__行为，可以对它自定义.
* 重载__new__实现单例.
__new__()方法接收到的参数依次是：

```
当前准备创建的类的对象；
类的名字；
类继承的父类集合；
类的方法集合。
```

##### 1.1.5.10. `__repr__`

* 内建函数str()和repr() (representation，表达，表示)或反引号操作符（``）可以方便地以字符串的方式获取对象的内容、类型、数值属性等信息。str()函数得到的字符串可读性好（故被print调用），而repr()函数得到的字符串通常可以用来重新获得该对象，通常情况下 `obj==eval(repr(obj))`这个等式是成立的。这两个函数接受一个对象作为其参数，返回适当的字符串。
* 事实上repr()和``做一样的事情，返回一个对象的“官方”字符串表示。其结果绝大多数情况下（不是所有）可以通过求值运算（内建函数eval()）重新得到该对象。str()则不同，它生成一个对象的可读性好的字符串表示，结果通常无法用eval()求值，但适合print输出。

```shell
>>> class D(object):
...     def __str__(self):
...         return "a __str__"
...     def __repr__(self):
...         return "a __repr__"
...
>>> dr = D()
>>> print dr
a __str__
>>> dr
a __repr__
>>> "%s" % dr
'a __str__'
>>> "%r" % dr
'a __repr__'
```

##### 1.1.5.11. `__slots__`

* 在Python中，每个类都有实例属性。默认情况下Python用一个字典来保存一个对象的实例属性。这非常有用，因为它允许我们在运行时去设置任意的新属性。
* 然而，对于有着已知属性的小类来说，它可能是个瓶颈。这个字典浪费了很多内存。Python不能在对象创建时直接分配一个固定量的内存来保存所有的属性。因此如果你创建许多对象（我指的是成千上万个），它会消耗掉很多内存。
* 不过还是有一个方法来规避这个问题。这个方法需要使用__slots__来告诉Python不要使用字典，而且只给一个固定集合的属性分配空间。

##### 1.1.5.12. `__str__`

* 用来返回对象的字符串表达式

#### 1.1.6. 枚举类

1. Python提供了Enum类。

#### 1.1.7. 元类

1. 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。创建class的方法就是使用type()函数。
2. 要创建一个class对象，type()函数依次传入3个参数：
```
class的名称；
继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；
class的方法名称与函数绑定，这里我们把函数fn绑定到方法名hello上。
```
3. 通过type()函数创建的类和直接写class是完全一样的，因为Python解释器遇到class定义时，仅仅是扫描一下class定义的语法，然后调用type()函数创建出class。
4. 正常情况下，我们都用class Xxx...来定义类，但是，type()函数也允许我们动态创建出类来，也就是说，`动态语言本身支持运行期动态创建类，这和静态语言有非常大的不同`，要在静态语言运行期创建类，必须构造源代码字符串再调用编译器，或者借助一些工具生成字节码实现，本质上都是动态编译，会非常复杂。


##### 1.1.7.1. --meteclass--

### 1.2. 继承

1. 通过多重继承，一个子类就可以同时获得多个父类的所有功能。

```
class Bat(Mammal, Flyable):
    pass

    def __init__(self):
        super().__init__()
```
2. 这种设计通常称之为MixIn。
3. MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过`多重继承`来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。
4. `所有类都继承object`.
5. 【注】派生类重新定义`__init__()`之后，需要显式地调用父类的`__init__()`函数,否则会出现`raise RuntimeError("thread.__init__() not called")`。

#### 1.2.1. 相关的方法

1. type()
2. isinstance()

### 1.3. 多态

### 1.4. with

1. 在python中实现了 `__enter__` 和`__exit__`方法，即支持上下文管理器协议.
2. 当with语句在开始运行时，会在上下文管理器对象上调用 `__enter__` 方法。with语句运行结束后，会在上下文管理器对象上调用 `__exit__` 方法

```python
class TmpTest:
    def __init__(self,filename):
        self.filename=filename
    def __enter__(self):
        self.f = open(self.filename, 'r')
        return self.f
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.f.close()

test=TmpTest('file')

with test as t:
    print ('test result: {}'.format(t))
```

