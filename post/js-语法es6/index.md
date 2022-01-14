# JS语法(ES6)


## 变量

1. $在js并不是特殊的存在，在Jquery中有$函数。一般地，在开发js脚本，而框架中必然要定义一些变量，而你又不希望用你脚本的人在定义变量的时候与你的变量重名，导致脚本中的变量被覆盖，这时，就可以使用$符号来进行标记一下哪些是你的脚本中的变量。
2. `this指的是调用函数的那个对象`，就是函数运行时所在的环境对象。
    a. 全局对象;
    b. 函数作为某个对象的方法调用，这时this就指这个上级对象；
    c. 作为构造函数调用:通过这个函数，可以生成一个新对象。这时，this就指这个新对象，如new function()；
    d. apply 调用:apply()是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。apply()的参数为空时，默认调用全局对象。

## 语法

### Throw 和 Try to Catch

1. try 语句使您能够测试代码块中的错误。
2. catch 语句允许您处理错误。
3. throw 语句允许您创建自定义错误。
4. finally 使您能够执行代码，在 try 和 catch 之后，无论结果如何。

### ES6中的class和static

1. 类就是实例的原型，new一个类,`在类中定义的方法，都会被实例继承`；如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
2. 父类的静态方法，可以被子类继承，super也可以调用。
3. JavaScript语言的传统方法是通过构造函数（属性在构造函数中定义）,定义并生成新对象,prototype 属性使您有能力向对象添加属性和方法（实例都会有这个属性和方法）（`构造器生成的实例.没有prototype属性`）。
4. 定义“类”的方法的时候,前面不需要加上function这个关键字,直接把函数定义放进去了就可以了。方法之间不需要逗号分隔,加了会报错。
```
console.log(typeof Person);//function
console.log(Person === Person.prototype.constructor);//true
```
5. `类的数据类型就是函数,类本身就指向构造函数`。
6. 类的`所有方法都定义在类的prototype属性上面`,可以通过prototype覆盖方法或者添加方法。
7. 在类的实例上面调用方法，其实就是调用原型上的方法。
8. ES6 明确规定， `Class 内部只有静态方法， 没有静态属性`但是ES7有了静态属性。
9. `定义实例属性， 只能写在类的constructor方法里面（使用this）或者function定义中（使用this）或者使用prototype属性`。
```
Object.keys（obj），返回一个数组，数组里是`该obj（实例）可被枚举的所有属性(不包括原型中的属性)`，但是for...in可以包含原型链中的可枚举属性。
Object.getOwnPropertyNames(obj)，返回一个数组，数组里是该obj上所有的实例属性。
```
10. `非Object都是实例，JS中的对象也是实例`。
11. constructor方法是类的构造函数是默认方法，`通过new命令生成对象实例时，自动调用该方法`。一个类必须有constructor方法，如果没有显式定义，一个默认的constructor方法会被添加。所以即使你没有添加构造函数,也是有默认的构造函数的。一般constructor方法默认返回实例对象this，但是也可以指定constructor方法返回一个全新的对象,让返回的实例对象不是该类的实例。
12. 类的构造函数，不使用new是没法调用的,即使你使用实例对象去调用也是不行的,这是它跟普通构造函数的一个主要区别。
13. 可以通过实例的__proto__属性为Class添加方法。
14. 这个类的名字是Expression而不是Expre，Expre只在Class的内部代码可用，指代当前类。
```
const Expression = class Expre{
 
    static getAge(){
        return '12';
    }
 
    getClassName(){
        return " ClassName1= " +Expre.name + " ClassName2= " +Expression.name;
    }
 
};
```

### 可枚举属性和不可枚举属性

1. 对象的属性分为可枚举和不可枚举之分，它们是由属性的enumerable值决定的。可枚举性决定了这个属性能否被for…in查找遍历到。
2.  js中基本包装类型的原型属性是不可枚举的，如Object, Array, Number等.
3. 对于通过直接的赋值和属性初始化的属性，该标识值默认为即为 true，对于通过 Object.defineProperty 等定义的属性，该标识值默认为 false。

## 函数（自定义）

### 异步编程的解决方案

#### 回调函数

```js
function f1(callback){
    setTimeout(function () {
        // f1的任务代码
        callback();
    }, 1000);
}
f1(f2);
```

1. 回调函数的优点是简单、容易理解和部署，`缺点是不利于代码的阅读和维护`，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。

#### 事件监听

```js
JQUERY的写法
f1.on('done', f2);
function f1(){
    setTimeout(function () {
        // f1的任务代码
        f1.trigger('done');
    }, 1000);
}
```

1. 这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"（Decoupling），有利于实现模块化。`缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰`。

#### 发布和订阅

我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。

```js
这个模式有多种实现，下面采用的是Ben Alman的Tiny Pub/Sub，这是jQuery的一个插件。
jQuery.subscribe("done", f2);
function f1(){
    setTimeout(function () {
        // f1的任务代码
        jQuery.publish("done");
    }, 1000);
}
jQuery.unsubscribe("done", f2); // 取消订阅
```

2. 这种方法的性质与"事件监听"类似，但是明显优于后者。因为我们可以通过查看"消息中心"，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。

#### Promises对象

1. Promises对象是CommonJS工作组提出的一种规范，目的是为异步编程提供统一接口。
2. 每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成`f1().then(f2);`。

```js
jquery的实现
function f1(){
    var dfd = $.Deferred();
    setTimeout(function () {
        // f1的任务代码
        dfd.resolve();
    }, 500);
    return dfd.promise;
}
```

3. 优点在于，回调函数变成了链式写法，程序的流程可以看得很清楚，而且有一整套的配套方法，可以实现许多强大的功能。
4. 指定多个回调函数：`f1().then(f2).then(f3);`
5. 再比如，指定发生错误时的回调函数：`f1().then(f2).fail(f3);`
6. 还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是编写和理解，都相对比较难。

#### async 和 await

1. `async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成`。
2. `await 只能出现在 async 函数中`。
3. `async 函数返回的是一个 Promise`。
4. 如果在函数中 return 一个直接量，async 会把这个直接量通过 Promise.resolve() 封装成 Promise 对象。then() 链来处理这个 Promise 对象。
5. 联想一下 Promise 的特点——无等待，所以在没有 await 的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且，绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数并无二致。
6. 因为 async 函数返回一个 Promise 对象，所以 await 可以用于等待一个 async 函数的返回值——这也可以说是 await 在等 async 函数，但要清楚，它等的实际是一个返回值。注意到 await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。
7. await 等到了它要等的东西，一个 Promise 对象，或者其它值，然后呢？await 是个运算符，用于组成表达式，await 表达式的运算结果取决于它等的东西。
8. 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
9. `如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果`。
10. 看到上面的阻塞一词，心慌了吧……放心，这就是 await 必须用在 async 函数中的原因。async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。
11. `async/await 的优势在于处理 then 链，与promise类似`。
12. 可以使用 Promise.all 来 await 多个 async（异步）函数。

```js
await Promise.all([anAsyncCall(), thisIsAlsoAsync(), oneMore()])
```

#### Generator

#### co
1. 把 Generator 和 Promise 封装，达到自动执行

### 默认参数

1. 使用对象解构(destructuring)模拟命名参数.

```js
function doSomething({ foo = 'Hi', bar = 'Yo!', baz = 13 }) {
  // ...
}

function doSomething({ foo = 'Hi', bar = 'Yo!', baz = 13 } = {}) {
  // ...
}
```

## 对象（前端）

### canvas对象标示一个html画布元素

1. <canvas> 标签只有两个属性—— width和height，这两个属性是可选的。当我们没有定义时，Canvas 的默认大小为300像素×150像素（宽×高，像素的单位是px）。但是，我们也可以使用HTML的高度和宽度属性来自定义尺寸。
2. canvas元素有两套尺寸：一个是元素本身的大小(通过CSS设置)，另一个是元素绘图表面的大小(通过canvas自身的width和height属性设置)。
注意：通过CSS修改width和height，只是改变了元素本身大小，对元素绘图表面的大小并无影响;
```
var Canvas = document.getElementById('tutorial');
```

| 父 | 子 |参数|功能 | 备注 |
|-- |-- | -- |--|-- |
| `ctx=Canvas.getContext(contextID)` | |'2d'| 返回一个用于在画布上绘图的环境 | [绘图参考](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D) |
| | .strokeStyle |描述画笔（绘制图形）颜色或者样式的属性|
| | .lineWidth | |
| | `.fillstyle` | 使用内部方式描述颜色和样式的属性。默认值是 #000 （黑色）|
| | `.font` |描述绘制文字时，当前字体样式的属性 |
| | `.textAlign`| |textAlign的值为center时候文本的居中是基于你在fillText的时候所给的x的值，也就是说文本一半在x的左边，一半在x的右边|
| | `.textBaseline`| top文本基线在文本块的顶部。hanging文本基线是悬挂基线。middle文本基线在文本块的中间。alphabetic文本基线是标准的字母基线。ideographic文字基线是表意字基线；如果字符本身超出了alphabetic 基线，那么ideograhpic基线位置在字符本身的底部。bottom文本基线在文本块的底部。 与 ideographic 基线的区别在于 ideographic 基线不需要考虑下行字母。默认值是 alphabetic。 |当前文本基线的属性 |
| | `void ctx.clearRect(x, y, width, height)` | 设置指定矩形区域内（以 点 (x, y) 为起点，范围是(width, height) ）所有像素变成透明，并擦除之前绘制的所有内容的方法 |
| | `void ctx.translate(x, y)` | 绘图原点移动 |
| | `void ctx.fillRect(x, y, width, height)` | 绘制填充矩形的方法。矩形的起点在 (x, y) 位置，矩形的尺寸是 width 和 height ，fillStyle 属性决定矩形的样式 |
| | `void ctx.scale(x, y)` | 根据 x 水平方向和 y 垂直方向，为canvas 单位添加缩放变换的方法 |
| | `void ctx.rotate(angle)` | 在变换矩阵中增加旋转的方法。角度变量表示一个顺时针旋转角度并且用弧度表示。旋转中心点一直是 canvas 的起始点。 如果想改变中心点，我们可以通过 translate() 方法移动 canvas 。|
| | beginPath() | 创建一个新的路径 |
| | moveTo(x,y) |将一个新的子路径的起始点移动到(x，y)坐标的方法|
| | lineTo(x,y) |使用直线连接子路径的终点到x，y坐标的方法（并不会真正地绘制） |
| | stroke()  | |
| | closePath() | |
| | save()    | 通过将当前状态放入栈中，保存 canvas 全部状态的方法|
| | restore() | 通过在绘图状态栈中弹出顶端的状态，将 canvas 恢复到最近的保存状态的方法。 |
| | `void ctx.fillText(text, x, y [, maxWidth])`|文本、x坐标，y坐标[,宽]（是字左下点的位置）|绘制文字|
| Canvas.height | |画布的高度 |
| Canvas.width | |画布的宽度 |

### 技巧

1. 绘制2D图形时，使用极坐标的话，可以通过旋转一定角度，那么x y坐标系跟着旋转。

#### 案例学习

1. [cuijing1031/Canvas-Gauge](https://github.com/cuijing1031/Canvas-Gauge)

### document对象

1. 每个载入浏览器的 HTML 文档都会成为 Document 对象。
2. Document 对象使我们可以从脚本中对 HTML 页面中的所有元素进行访问。

| 属性和方法 | 功能 | 备注 |
| -- | -- | -- |
| document.addEventListener() | 用于向文档添加事件句柄 | 具体事件可查看html笔记中DOM事件 |

### element对象
1. 指定元素。



### [window对象](http://www.w3school.com.cn/jsref/dom_obj_window.asp)
1. Window 对象表示浏览器中打开的窗口。

| 属性和方法 | 功能 |
| -- | -- |
| window.requestAnimationFrame(animate)| 告诉浏览器您希望执行动画并请求浏览器在下一次重绘之前调用指定的函数来更新动画。该方法使用一个回调函数作为参数，这个回调函数会在浏览器重绘之前调用。|
| window.location.href | 当前页面的路由，可以修改这个值跳转到其他页面|
| window.ScrollTo       | 当前页面滚动 |

## 内置方法

encodeURIComponent() 函数可把字符串作为URI组件进行编码，返回正常打印看不见的字符。

### JavaScript中Get和Set访问器

```
标准用法
function Field(val){  
    this.value = val;  
} 
Field.prototype = {  
    get value(){  
        return this._value;  
    },  
    set value(val){  
        this._value = val;  
    }
};
var field = new Field("test");
field.value="test2";
//field.value will now return "test2"
```

### constructor 

是一种用于创建和初始化class创建的对象的特殊方法

### 数学计算

|方法　|　功能　|
|--|--|
|parseInt　|　丢弃小数部分　|
|Math.ceil　|向上取整　|
|Math.round　|四舍五入　|
|Math.floor |向下取整　|
| % | 取余　｜

### apply() 方法接受数组形式的参数

```
Math.max.apply(null, [1,2,3]); // 返回数组最大值
Math.min.apply(null, [1,2,3]); // 返回数组最小值
```

### eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码

## 声明

1. "use strict" 的目的是指定代码在严格条件下执行,严格模式下你不能使用未声明的变量。

