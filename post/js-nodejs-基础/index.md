# JS-NODEJS-基础


参考[ElemeFE/node-interview](https://github.com/ElemeFE/node-interview/tree/master/sections/zh-cn)
参考[挑战一轮大厂后的面试总结 (含六个方向) - nodejs 篇](https://juejin.im/post/5e53cf886fb9a07c91101642)

摘自[图解Javascript原型链](https://cnodejs.org/topic/55accdeab4ab1d7d02bf0d8c)

# 基础
1. 其他皆对象。
2. 但是Js中并没有类（class）；`Js是基于原型（prototype-based）来实现的面向对象（OOP）的编程范式的`，但并不是所有的对象都拥有prototype这一属性.在 ES2015/ES6 中引入了 class 关键字，但那只是语法糖，JavaScript 仍然是基于原型的）.
3. `prototype是每个function定义时自带的属性`，另外还有length。但是Js中function本身也是对象,直接new [function]()生成的对象（`没有prototype`）的构造器就是这个[function].
1. __proto__（对象的隐式原型,指向其父类的prototype）与prototype（显式原型）.
1. __proto__是每个对象都有的一个属性，而prototype是函数才会有的属性。
2. __proto__指向的是当前对象的原型对象，而prototype指向的是以当前函数作为构造函数构造出来的对象的原型对象。
1. 实际上Function就是一个用于构造函数类型变量的类，或者说是函数类型实例的构造函数（constructor）；与之相似有的Object或String、Number等，都是Js内置类型实例的构造函数。比较特殊的是Object，它用于生成对象类型，其简写形式为{}.
1. prototype和length是每一个函数类型自带的两个属性，而其它非函数类型并没有（开头的例子已经说明），这一点之所以比较容易被忽略或误解，是因为`所有类型的构造函数本身也是函数`，所以它们自带了prototype属性
```
var Person = function(){};
Person.prototype.type = 'Person';
Person.prototype.maxAge = 100;

var p = new Person();
console.log(p.maxAge);
p.name = 'rainy';

Person.prototype.constructor === Person;  //=> true
p.__proto__ === Person.prototype;         //=> true
console.log(p.prototype);                 //=> undefined
```
1. erson是一个函数类型的变量，因此自带了prototype属性，prototype属性中的constructor又指向Person本身；通过new关键字生成的Person类的实例p1，通过__proto__属性指向了Person的原型。这里的__proto__只是为了说明实例p1在内部实现的时候与父类之间存在的关联（指向父类的原型），在实际操作过程中实例可以直接通过.获取父类原型中的属性，从而实现了继承的功能。
2. prototype对象也有__proto__属性，向上追溯一直到null.
1. 当谈到继承时，JavaScript 只有一种结构：对象。每个实例对象（ object ）都有一个私有属性（称之为 __proto__ ）指向它的构造函数的原型对象（prototype ）。该原型对象也有一个自己的原型对象( __proto__ ) ，层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。
2. 几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

## 类型判断

### typeof 判断类型
1. 6大原始类型object(null)、undefined、string、number、boolean和function
```js
  typeof undefined; // undefined
  typeof []; // object
  typeof '123'; // string
  typeof new String('123'); // object
  typeof null; // object ***
  function f1(){   // new Function("console.log('This is function f3!');");
    console.log('This is function f1!');
  }
  typeof(f1);  //=> 'function'
  typeof(String)    // function
  typeof(Number)  // function
  typeof(Function)  // function


```

### instanceof 判断对象的__proto__（以及__proto__.__proto__…[原型链]）是否包含构造函数的原型(),判断是否是某个类型的实例

```
  const s = new String('123');
  s instanceof String; // true
  s instanceof Object; // true
  s.__proto__ = Object.prototype;
  s instanceof String; // false
  s instanceof Object; // true

```

### constructor属性

### toString方法

### 内置Symbol接口

## 作用域

### let  const 和 var

1. ES6新增了let命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在`let命令所在的代码块`内有效。
2. for循环的计数器，就很合适使用let命令。
```
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```
3. let不像var那样会发生“变量提升”现象。所以，变量一定要在声明后使用，否则报错。
4. const声明一个只读的常量。一旦声明，常量的值就不能改变。
5. const的作用域与let命令相同：只在声明所在的块级作用域内有效。

