# 编程-规范(NODE为主)


## 编程思想

### 面向过程

### 面向对象

1. 《重构:改善既有代码的设计》9.0
2. 《Effective Java 中文版》8.9
3. 《深入浅出面向对象分析与设计（中文版）》8.8
4. 《UML和模式应用》8.1

#### 继承

#### 封装

#### 多态

### 函数式编程

1. [函数式编程入门教程](https://www.ruanyifeng.com/blog/2017/02/fp-tutorial.html).
2. [Ramda 一款实用的 JavaScript 函数式编程库](https://ramda.cn/)

## 变量

1. 不要定义没有用到的变量；
2. 不要使用只有自己知道的缩写，最好使用完整的单词（有具体含义）；
3. 变量名中不要指定类型；
4. 常量使用全大写声明在显眼位置；
5. 对于求值变量，要进行兜底，万一有取不到的情况，就会报错。

## 函数

1. 对于返回true或者false的函数，最好以should/is/can/has开头；
2. 功能函数使用纯函数；
3. 调用函数时，传参有说明；
4. 动作函数要以动词开头；
5. 一个函数只能完成一个功能；
6. 优先使用函数式编程，例如`使用map、filter、find、some、any、forEach替代for循环`；
7. 尽量使用switch或者数组替代if else：
```
let handler = {
    1: () => {},
    2: () => {},
    3: () => {},
    default: () => {}
}
handler[a]() || handler['default']()
```
8. [JS]尽量使用ES6，可以的话使用ES7
```
(1)使用箭头函数
let foo = () => {
    // code
}
不要使用
function foo() {
    //code
}
(2)连接字符串使用模板字符，不要使用加号
`${varname}`
(3)使用结构赋值，不要使用传统赋值
{，} = var；
(4)尽量使用类class
class Human extends Mammal {
    cinstructor(){
        
    }
}
```
