# JAVA-基础


[JAVA基本语法](https://www.w3cschool.cn/java/java-basic-syntax.html)
[参考速学堂](https://www.sxt.cn/Java_jQuery_in_action/seven-array-declaration.html)

# 面向对象

1. *核心：将物理事物用代码抽象出来，拟物化编程*。
2. 一个Java程序可以认为是一系列对象的集合，而这些对象通过调用彼此的方法来协同工作。

# 基本概念



# 基本语法

1. 大小写敏感：Java是大小写敏感的，这就意味着标识符Hello与hello是不同的。
2. 类名：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，那么每个单词的首字母应该大写，例如 MyFirstJavaClass 。
3. 方法名：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
4. 源文件名：`源文件名必须和类名相同`。当保存文件的时候，你应该使用类名作为文件名保存（切记Java是大小写敏感的），文件名的后缀为.java。（如果文件名和类名不相同则会导致编译错误）。
5. 主方法入口：所有的Java 程序由public static void main(String args[])方法开始执行。

# 数据类型

## 基本类型

### 浮点数
1. 浮点常量默认是double的，单精度保存小数点7位；
2. 浮点数是不精确的，避免浮点数之间的比较。
3. 使用高精度，可使用java.math中的类 BigDecimal（不可变的、任意精度的有符号十进制数。）和类 BigInteger（不可变的任意精度的整数。）

### Boolean
1. 按bit位占内存.

## 引用类型
1. 字符串是对象。

# 控制语句
1. 带标签的break和continue
```
public class Test18 {
    public static void main(String args[]) {
        outer: for (int i = 101; i < 150; i++) {
            for (int j = 2; j < i / 2; j++) {
                if (i % j == 0){
                    continue outer;
                }
            }
            System.out.print(i + "  ");
        }
    }
}
```
2. `方法重载`（方法名称相同，形式参数类型不同，形式参数个数不同，形式参数顺序不同，可以重载）。
3. 递归（递归头和递归体），尽量避免使用递归，效率低下。一般都可以使用循环或if替代。

# String

1. 最常用。
2. String类又称作不可变字符序列。
3. Java没有内置的字符串类型，而是在标准Java类库中提供了一个预定义的类String，`每个用双引号括起来的字符串都是String类的一个实例`。

## 常量池

1. 全局字符串常量池(`String Pool`)
    全局字符串常量池中存放的内容是在类加载完成后存到String Pool中的，`在每个VM中只有一份`，存放的是字符串常量的引用值(在堆中生成字符串对象实例)。
2. class文件常量池(`Class Constant Poo`l)
    `class常量池是在编译的时候每个class都有的，在编译阶段，存放的是常量(文本字符串、final常量等)和符号引用`。
3. 运行时常量池(`Runtime Constant Pool`)
    运行时常量池是在类加载完成之后，将每个class常量池中的符号引用值转存到运行时常量池中，也就是说，`每个class都有一个运行时常量池`，类在解析之后，将符号引用替换成直接引用，与全局常量池中的引用值保持一致。

## 内存分析
1. `String str1 = "abc";在堆中会有一个“abc”实例，全局String Pool中存放着“abc”的一个引用值`.
2. `String str2 = new String("def");``生成两个实例`，一个是“def”的实例对象，并且String Pool中存储一个“def”的引用值，还有一个是new出来的一个“def”的实例对象，与前边那个是不同的实例;`str2.intern();`返回String Pool中“def”的引用值，如果没有就将str2的引用值添加进去.

# 数组

1. 数组是相同类型数据的有序集合。
2. 长度是确定的。数组一旦被创建，它的大小就是不可以改变的。
2. 其元素必须是相同类型，不允许出现混合类型。
3. 数组类型可以是任何数据类型，包括基本类型和引用类型。
4. 数组变量属引用类型，数组也可以看成是对象，`数组中的每个元素相当于该对象的成员变量`。数组本身就是对象，Java中对象是在堆中的，因此数组无论保存原始类型还是其他对象类型，`数组对象本身是在堆中存储的`。

## 声明

```js
type[]   arr_name; //（推荐使用这种方式）
type    arr_name[];
```

1. 声明的时候并没有实例化任何对象，只有在实例化数组对象时，JVM才分配空间，这时才与长度有关。
2. 声明一个数组的时候并没有数组真正被创建。
3. 构造一个数组，必须指定长度。

```js
int[] s = null; // 声明数组；
s = new int[10]; // 给数组分配空间；

Man[] mans;  //声明引用类型数组； 
mans = new Man[10];  //给引用类型数组分配空间；
```

## 初始化

#### 静态初始化
```
int[] a = { 1, 2, 3 };// 静态初始化基本类型数组；
Man[] mans = { new Man(1, 1), new Man(2, 2) };// 静态初始化引用类型数组；
```

#### 动态初始化

#### 默认初始化
1.   数组是引用类型，它的元素相当于类的实例变量，因此数组一经分配空间，其中的每个元素也被按照实例变量同样的方式被隐式初始化。

## 遍历

1. 通过for和下标进行遍历（读取或修改）。
2. 增强for循环for-each是JDK1.5新增加的功能，`专门用于读取数组或集合中所有的元素`，即对数组进行遍历，不能修改。
```
String[] ss = { "aa", "bbb", "ccc", "ddd" };
for (String temp : ss) {
    System.out.println(temp);
}
```
## 拷贝
1. `System类里也包含了一个static void arraycopy(object src，int srcpos，object dest， int destpos，int length)方法`，该方法可以将src数组里的元素值赋给dest数组的元素，其中srcpos指定从src数组的第几个元素开始赋值，length参数指定将src数组的多少个元素赋给dest数组的元素。

## java.util.Arrays类

1. 包含了常用的数组操作，方便我们日常开发。Arrays类包含了：排序、查找、填充、打印内容等常见的操作。
```
Arrays.toString()    // 打印
Arrays.sort()    // 排序
Arrays.binarySearch(a, 12)    // 返回元素索引
Arrays.fill(a, 2, 4, 100);  //将2到4索引的元素替换为100;
```
2. 通过实现Comparable接口的compareTo方法，可以自定义排序规则。

## 多维数组

1. 实际开发中用的非常少。最多到二维数组(学习容器后，`我们一般使用容器`，二维数组用的都很少)。
```
public class Test {
    public static void main(String[] args) {
        // Java中多维数组的声明和初始化应按从低维到高维的顺序进行
        int[][] a = new int[3][];
        a[0] = new int[2];
        a[1] = new int[4];
        a[2] = new int[3];
        // int a1[][]=new int[][4];//非法
    }
}
        int[][] a = new int[3][];
        // a[0] = {1,2,5}; //错误，没有声明类型就初始化
        a[0] = new int[] { 1, 2 };
        a[1] = new int[] { 2, 2 };
        a[2] = new int[] { 2, 2, 3, 4 };
```

## 数组存对象

1. 此处基本数据类型”1001”，本质不是Object对象。`JAVA编译器会自动把基本数据类型“自动装箱”成包装类对象（需要对象的场合）`。
```
Object[] a1 = {1001,"高淇",18,"讲师","2006-2-14"};
Object[] a2 = {1002,"高小七",19,"助教","2007-10-10"};
Object[] a3 = {1003,"高小琴",20,"班主任","2008-5-5"};
```
2. 通常情况下，我们`使用javabean`实现对象数组。

# 异常机制

1. 就是当程序出现错误，程序安全退出的机制。
2. 在Java的异常处理机制中，引进了很多用来描述和处理异常的类，称为异常类。异常类定义中包含了该类异常的信息和对异常进行处理的方法。
3. `所谓异常处理，就是指程序在出现问题时依然可以正确的执行完`。

## 异常处理（采用面向对象的方式来处理异常）

1. `抛出异常`：在执行一个方法时，如果发生异常，则这个方法生成代表该异常的一个对象，停止当前执行路径，并把异常对象提交给JRE。
2. `捕获异常`：JRE得到该异常后，寻找相应的代码来处理该异常。JRE在方法的调用栈中查找，从生成异常的方法开始回溯，直到找到相应的异常处理代码为止。
3. 异常的处理方式有两种：使用“try/catch”捕获异常、使用“throws”声明异常。

### 捕获异常
1. 捕获异常是通过3个关键词来实现的：try-catch-finally。用try来执行一段程序，如果出现异常，系统抛出一个异常，可以通过它的类型来捕捉(catch)并处理它，最后一步是通过finally语句为异常处理提供一个统一的出口，finally所指定的代码都要被执行(catch语句可有多条;finally语句最多只能有一条，根据自己的需要可有可无)。
2. 一个try语句必须带有至少一个catch语句块或一个finally语句块 。
3. 如果异常类之间有继承关系，在顺序安排上需注意。`越是顶层的类，越放在下面`，再不然就直接把多余的catch省略掉。 也就是先捕获子类异常再捕获父类异常。
4. 有些语句，不管是否发生了异常，都必须要执行，那么就可以把这样的语句放到finally语句块中。
5. 通常在finally中关闭程序块已打开的资源，比如：关闭文件流、释放数据库连接等。
7. `即使try和catch块中存在return语句，finally语句也会执行。是在执行完finally语句后再通过return退出`。
8. finally语句块只有一种情况是不会执行的，那就是在执行finally之前遇到了System.exit(0)结束程序运行。

#### try-with-resources 

1. 是 JDK 7 中一个新的异常处理机制，它能够很容易地关闭在 try-catch 语句块中使用的资源。
2. 所谓的资源（resource）是指在程序完成后，必须关闭的对象。try-with-resources 语句确保了每个资源在语句结束时关闭。所有实现了 java.lang.AutoCloseable 接口（其中，它包括实现了 java.io.Closeable 的所有对象），可以使用作为资源。
```
public class Main2 {    
    public static void main(String[] args) {
        try(ResourceSome some = new ResourceSome();
             ResourceOther other = new ResourceOther()) {
            some.doSome();
            other.doOther();
        } catch(Exception ex) {
            ex.printStackTrace();
        }
    }
}

class ResourceSome implements AutoCloseable {
    void doSome() {
        System.out.println("do something");
    }
    @Override
    public void close() throws Exception {
        System.out.println("some resource is closed");
    }
}

class ResourceOther implements AutoCloseable {
    void doOther() {
        System.out.println("do other things");
    }
    @Override
    public void close() throws Exception {
        System.out.println("other resource is closed");
    }
}
最终输出为：

do something
do other things
other resource is closed
some resource is closed
在 try 语句中越是最后使用的资源，越是最早被关闭。
```
在 JDK 9 已得到改进。如果你已经有一个资源是 final 或等效于 final 变量,您可以在 try-with-resources 语句中使用该变量，而无需在 try-with-resources 语句中声明一个新变量。

### 声明异常
1. 如果一个方法中可能产生某种异常，但是并不能确定如何处理这种异常，则应根据异常规范在方法的首部声明该方法可能抛出的异常。
2. 如果一个方法抛出多个已检查异常，就必须在方法的首部列出所有的异常，之间以逗号隔开。
3. `方法重写`中声明异常原则：子类重写父类方法时，如果父类方法有声明异常，那么子类声明的异常范围不能超过父类声明的范围。
4. throws和throw

## 异常分类

1. 所有异常对象都是`派生于Throwable类(java.lang.Throwable)的一个实例`。如果内置的异常类不能够满足需要，还可以创建自己的异常类。
2. Java对异常进行了分类，不同类型的异常分别用不同的Java类表示，`所有异常的根类为java.lang.Throwable`，`Throwable下面又派生了两个子类：Error和Exception`。

### Error

1. 表示代码运行时` JVM(Java 虚拟机)`出现的问题.
2. Error表明系统JVM已经处于不可恢复的崩溃状态中。

### Exception

1. Exception是程序本身能够处理的异常.
2. Exception类是所有异常类的父类，其子类对应了各种各样可能出现的异常事件。 通常Java的异常可分为：
    1. RuntimeException 运行时异常
    2. CheckedException 已检查异常

#### RuntimeException

1. 由系统自动检测并将它们交给缺省的异常处理程序(用户可不必对其处理)。
2. 这类异常通常是由`编程错误`导致的，所以在编写程序时，并不要求必须使用异常处理机制来处理这类异常,经常需要通过增加“`逻辑处理`来避免这些异常”。

#### CheckedException

1. 这类异常在编译时就必须做出处理，否则无法通过编译。

## 自定义异常

1. 自定义异常类只需从Exception类或者它的子类派生一个子类即可。
3. 自定义异常类如果继承Exception类，则为受检查异常，必须对其进行处理;如果不想处理，可以让自定义异常类继承运行时异常RuntimeException类。
3. 习惯上，自定义异常类应该包含2个构造器：`一个是默认的构造器，另一个是带有详细信息的构造器`。
1. 要避免使用异常处理代替错误处理，这样会降低程序的清晰性，并且效率低下。
2. 处理异常不可以代替简单测试---只在异常情况下使用异常机制。
3. 不要进行小粒度的异常处理---应该将整个任务包装在一个try语句块中。
4. 异常往往在高层处理。
