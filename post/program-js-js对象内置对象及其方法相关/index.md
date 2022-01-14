# JS标准内置对象(全局作用域里的对象)


参考[JavaScript 标准内置对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)

`感谢`

```xml
作者：小黎也
链接：https://juejin.im/post/5e523e726fb9a07c9a195a95
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 1. 值属性

1. 这些全局属性`返回一个简单值`，`这些值没有自己的属性和方法`，就是一个常数。

## 1.1. Infinity

## 1.2. NaN

## 1.3. undefined

## 1.4. null 字面量

## 1.5. globalThis

# 2. 函数属性

1. `全局函数可以直接调用`，不需要在调用时指定所属对象，`执行结束后会将结果直接返回给调用者`。

## 2.1. eval()

## 2.2. uneval()

## 2.3. isFinite()

## 2.4. isNaN()

## 2.5. parseFloat()

## 2.6. parseInt()

## 2.7. decodeURI()

## 2.8. decodeURIComponent()

## 2.9. encodeURI()

## 2.10. encodeURIComponent()

# 3. 基本对象

1. 基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象。

## 3.1. Object

1. Object 构造函数创建一个对象包装器。
2. `JavaScript 中的所有对象都来自 Object`；所有对象从 Object.prototype 继承方法和属性，尽管它们可能被覆盖。例如，其他构造函数的原型将覆盖 constructor 属性并提供自己的 toString() 方法。`Object 原型对象的更改将传播到所有对象，除非受到这些更改的属性和方法将沿原型链进一步覆盖`。

### 3.1.1. Object 构造函数 的属性

1. Object.length 值为 1。
2. Object.prototype 可以为所有 Object 类型的对象添加属性。

### 3.1.2. Object 构造函数 的方法

#### 3.1.2.1. Object.assign()

1. 通过复制一个或多个对象来创建一个新的对象。
2. `注意`：Object.assign() 只是一级属性复制，比浅拷贝`多深拷贝了一层`而已，其内层还是引用地址。

#### 3.1.2.2. Object.create()

1. 使用指定的原型对象和属性创建一个新对象。
2. `const pureObject = Object.create(null);`您可以创建一个 100％ 纯对象，它不会从 Object 继承任何属性或方法.

#### 3.1.2.3. Object.defineProperty()

给对象添加一个属性并指定该属性的配置。

#### 3.1.2.4. Object.defineProperties()

给对象添加多个属性并分别指定它们的配置。

#### 3.1.2.5. Object.entries()

返回给定对象自身可枚举属性的 [key, value] 数组。

#### 3.1.2.6. Object.freeze()

冻结对象：其他代码不能删除或更改任何属性。

#### 3.1.2.7. Object.getOwnPropertyDescriptor()

返回对象指定的属性配置。

#### 3.1.2.8. Object.getOwnPropertyNames()

返回一个数组，它包含了指定对象所有的可枚举或不可枚举的属性名。

#### 3.1.2.9. Object.getOwnPropertySymbols()

返回一个数组，它包含了指定对象自身所有的符号属性。

#### 3.1.2.10. Object.getPrototypeOf()

返回指定对象的原型对象。

#### 3.1.2.11. Object.is()

比较两个值是否相同。所有 NaN 值都相等（这与==和===不同）。

#### 3.1.2.12. Object.isExtensible()

判断对象是否可扩展。

#### 3.1.2.13. Object.isFrozen()

判断对象是否已经冻结。

#### 3.1.2.14. Object.isSealed()

判断对象是否已经密封。

#### 3.1.2.15. Object.keys()

返回一个包含所有给定对象自身可枚举属性名称的数组。

#### 3.1.2.16. Object.preventExtensions()

防止对象的任何扩展。

#### 3.1.2.17. Object.seal()

防止其他代码删除对象的属性。

#### 3.1.2.18. Object.setPrototypeOf()

设置对象的原型（即内部 [[Prototype]] 属性）。

#### 3.1.2.19. Object.values()

返回给定对象自身可枚举值的数组。

### 3.1.3. Object 实例的方法

#### 3.1.3.1. Object.prototype.hasOwnProperty()

返回一个布尔值 ，表示某个对象是否含有指定的属性，而且此属性非原型链继承的。

#### 3.1.3.2. Object.prototype.isPrototypeOf()

返回一个布尔值，表示指定的对象是否在本对象的原型链中。

#### 3.1.3.3. Object.prototype.propertyIsEnumerable()

1. 判断指定属性是否可枚举，内部属性设置参见 ECMAScript [[Enumerable]] attribute 。
2. 如果判断的属性`存在于Object对象的原型内`，不管它是否可枚举都会返回false。

#### 3.1.3.4. Object.prototype.toLocaleString()

直接调用 toString()方法。

#### 3.1.3.5. Object.prototype.toString()

返回对象的字符串表示。

#### 3.1.3.6. Object.prototype.valueOf()

返回指定对象的原始值。

## 3.2. Symbol

1. 在 symbol 出现之前，对象键只能是字符串，如果试图使用非字符串值作为对象的键，那么该值将被强制转换为字符串。
2. Symbol 的作用非常的专一，换句话说其设计出来就只有一个目的——作为对象属性的唯一标识符，防止对象属性冲突发生。
3. [简单了解 ES6/ES2015 Symbol() 方法](https://www.zhangxinxu.com/wordpress/2018/04/known-es6-symbol-function/).

# 4. 数字和日期对象

## 4.1. Number

### 4.1.1. 实例方法

#### 4.1.1.1. Number.prototype.toFixed()
1. 使用定点表示法（小数点位置固定）来格式化一个数值
2. toFixed 对于四舍六入没问题，但对于尾数是 5 的处理就非常诡异（向5靠近）。
```js
(1.235).toFixed(2)
"1.24"
(1.245).toFixed(2)
"1.25"
(1.255).toFixed(2)
"1.25"
(1.265).toFixed(2)
"1.26"
(1.275).toFixed(2)
"1.27"
```

### 4.1.2. Number实战
1. 10 进制转其他进制：Number(val).toString([2,8,10,16])
其他进制转成10进制：Number.parseInt("1101110",[2,8,10,16])
其他进制互转：先将其他进制转成 10 进制，在把 10 进制转成其他进制

# 5. 字符串

# 6. 可索引的集合对象

1. `表示按照索引值来排序的数据集合`，包括数组和类型数组，以及类数组结构的对象。

## 6.1. Array

1. Array 既是一个数组，也是一个字典。

### 6.1.1. Array 构造函数的属性

### 6.1.2. Array 构造函数的方法

#### 6.1.2.1. Array.from()

从类数组对象或者可迭代对象中创建一个新的数组实例。

#### 6.1.2.2. Array.isArray()

用来判断某个变量是否是一个数组对象。

#### 6.1.2.3. Array.of()

根据一组参数来创建新的数组实例，支持任意的参数数量和类型。

### 6.1.3. Array 实例的属性

### 6.1.4. Array 实例的方法

#### 6.1.4.1. 修改器方法

下面的这些方法会改变调用它们的对象自身的值：

##### 6.1.4.1.1. Array.prototype.pop()

删除数组的最后一个元素，并返回这个元素。

##### 6.1.4.1.2. Array.prototype.push()

在数组的末尾增加一个或多个元素，并返回数组的新长度。

##### 6.1.4.1.3. Array.prototype.reverse()

颠倒数组中元素的排列顺序，即原先的第一个变为最后一个，原先的最后一个变为第一个。

##### 6.1.4.1.4. Array.prototype.shift()

删除数组的第一个元素，并返回这个元素。

##### 6.1.4.1.5. Array.prototype.sort()

对数组元素进行排序，并返回当前数组。

##### 6.1.4.1.6. Array.prototype.splice()

在任意的位置给数组添加或删除任意个元素。

##### 6.1.4.1.7. Array.prototype.unshift()

在数组的开头增加一个或多个元素，并返回数组的新长度。

#### 6.1.4.2. 访问方法

下面的这些方法绝对不会改变调用它们的对象的值，只会返回一个新的数组或者返回一个其它的期望值。

##### 6.1.4.2.1. Array.prototype.concat()

返回一个由当前数组和其它若干个数组或者若干个非数组值组合而成的新数组。

##### 6.1.4.2.2. Array.prototype.join()

连接所有数组元素组成一个字符串。

##### 6.1.4.2.3. Array.prototype.slice()

抽取当前数组中的一段元素组合成一个新数组。

##### 6.1.4.2.4. Array.prototype.toString()

返回一个由所有数组元素组合而成的字符串。遮蔽了原型链上的 Object.prototype.toString() 方法。

##### 6.1.4.2.5. Array.prototype.toLocaleString()

返回一个由所有数组元素组合而成的本地化后的字符串。遮蔽了原型链上的 Object.prototype.toLocaleString() 方法。

##### 6.1.4.2.6. Array.prototype.indexOf()

返回数组中第一个与指定值相等的元素的索引，如果找不到这样的元素，则返回 -1。

##### 6.1.4.2.7. Array.prototype.lastIndexOf()

返回数组中最后一个（从右边数第一个）与指定值相等的元素的索引，如果找不到这样的元素，则返回 -1。

#### 6.1.4.3. 迭代方法

在下面的众多遍历方法中，有很多方法都需要指定一个回调函数作为参数。在每一个数组元素都分别执行完回调函数之前，数组的 length 属性会被缓存在某个地方，所以，如果你在回调函数中为当前数组添加了新的元素，那么那些新添加的元素是不会被遍历到的。此外，如果在回调函数中对当前数组进行了其它修改，比如改变某个元素的值或者删掉某个元素，那么随后的遍历操作可能会受到未预期的影响。总之，不要尝试在遍历过程中对原数组进行任何修改，虽然规范对这样的操作进行了详细的定义，但为了可读性和可维护性，请不要这样做。

##### 6.1.4.3.1. Array.prototype.forEach()

1. 为数组中的每个元素执行一次回调函数。
2. `回调函数是异步执行的，但是整个forEach是同步的`。

##### 6.1.4.3.2. Array.prototype.every()

如果数组中的每个元素都满足测试函数，则返回 true，否则返回 false。

##### 6.1.4.3.3. Array.prototype.some()

如果数组中至少有一个元素满足测试函数，则返回 true，否则返回 false。

##### 6.1.4.3.4. Array.prototype.filter()

将所有在过滤函数中返回 true 的数组元素放进一个新数组中并返回。

##### 6.1.4.3.5. Array.prototype.map()

返回一个由回调函数的返回值组成的新数组。

##### 6.1.4.3.6. Array.prototype.reduce()

从左到右为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。

##### 6.1.4.3.7. Array.prototype.reduceRight()

从右到左为每个数组元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。

### 6.1.5. Array 实战

1. [JavaScript 高性能数组去重](https://www.cnblogs.com/wisewrong/p/9642264.html).
2. `将数组放进对象中，可实现去重，而且效率高；若key为数字，还能天然排序（使用Object.Values）`.
3. 在不重新给数组赋值的情况下，清空或截断数组的最简单方法是更改其 length 属性值。
4. 可以使用对象解构将数组项分配给各个变量.

```js
const csvFileLine = '1997,John Doe,US,john@doe.com,New York';
const { 2: country, 4: state } = csvFileLine.split(',');
```

## 6.2. Uint8Array

1. 表示一个 8 位无符号整型数组，创建时内容被初始化为 0。创建完后，可以以对象的方式或使用数组下标索引的方式引用数组中的元素。

### 6.2.1. Node 中 Buffer

1. `Node 中 Buffer 类是 JavaScript 语言内置的 Uint8Array 类的子类`。
2. Buffer 转 Json。

# 7. 使用键的集合对象

## 7.1. Set

1. Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

### 7.1.1. Set 实战

#### 7.1.1.1. 集合运算

```js
var a = [1, 2, 3, 4, 5];
var b = [2, 4, 6, 8, 10];
console.log("数组a：", a);
console.log("数组b：", b);

var sa = new Set(a);
var sb = new Set(b);

// 交集
let intersect = a.filter((x) => sb.has(x));

// 差集
let minus = a.filter((x) => !sb.has(x));

// 补集
let complement = [
  ...a.filter((x) => !sb.has(x)),
  ...b.filter((x) => !sa.has(x)),
];

// 并集
let unionSet = Array.from(new Set([...a, ...b]));

console.log("a与b的交集：", intersect);
console.log("a与b的差集：", minus);
console.log("a与b的补集：", complement);
console.log("a与b的并集：", unionSet);
```

# 8. 结构化数据

1. 这些对象用来表示和操作`结构化`的缓冲区数据，或使用 JSON （JavaScript Object Notation）编码的数据。

## 8.1. ArrayBuffer

1. ArrayBuffer 对象用来表示通用的、固定长度的原始二进制数据缓冲区。
2. 它是一个字节数组，通常在其他语言中称为“byte array”。
3. 你`不能直接操作 ArrayBuffer 的内容，而是要通过类型数组对象或 DataView 对象来操作`，它们会将缓冲区中的数据表示为特定的格式，并通过这些格式来读写缓冲区的内容。

## 8.2. DataView

1. DataView 视图是一个可以从 二进制ArrayBuffer 对象中读写多种数值类型的底层接口，使用它时，不用考虑不同平台的字节序问题。

## 8.3. JSON

1. JSON.stringify 不仅可以简单地将对象转化为字符串。你也可以用它来格式化JSON输出：第三个参数用来指定空格的数目。
2. JSON.parse() 可以接受第二个参数，它可以在返回之前转换对象值。比如这例子中，将返回对象的属性值大写。
```js
const user = {
  name: 'John',
  email: 'john@awesome.com',
  plan: 'Pro'
};
 
const userStr = JSON.stringify(user);
 
const newUserStr = JSON.parse(userStr, (key, value) => {
  if (typeof value === 'string') {
    return value.toUpperCase();
  }
  return value;
});
 
console.log(newUserStr); //{name: "JOHN", email: "JOHN@AWESOME.COM", plan: "PRO"} 
```
3. JSON.stringify() 可以带两个额外的参数，第一个是替换函数，第二个间隔字符串，用作隔开返回字符串。
value ： 将要转为JSON字符串的javascript对象。
replacer ：该参数可以是多种类型,如果是一个函数,则它可以改变一个javascript对象在字符串化过程中的行为, 如果是一个包含 String 和 Number 对象的数组,则它将作为一个白名单.只有那些键存在域该白名单中的键值对才会被包含进最终生成的JSON字符串中.如果该参数值为null或者被省略,则所有的键值对都会被包含进最终生成的JSON字符串中。
space ：该参数可以是一个 String 或 Number 对象,作用是为了在输出的JSON字符串中插入空白符来增强可读性. 如果是Number对象, 则表示用多少个空格来作为空白符; 最大可为10,大于10的数值也取10.最小可为1,小于1的数值无效,则不会显示空白符. 如果是个 String对象, 则该字符串本身会作为空白符,字符串最长可为10个字符.超过的话会截取前十个字符. 如果该参数被省略 (或者为null), 则不会显示空白符
4. 利用toJSON方法,我们可以修改对象转换成JSON的默认行为。
```js
var obj = {
    foo: 'foo',
    toJSON:function(){
        return 'bar';
    }
}
JSON.stringify(obj);//'"bar"'
JSON.stringify({x:obj});//'{"x":"bar"}'
```

# 9. 控制抽象对象

## 9.1. Promise

1. Promise 对象用于表示一个异步操作的最终状态（完成或失败），以及该异步操作的结果值。`Promise 是一个绑定了回调的对象，而不是将回调传进函数内部`。

### 9.1.1. 使用Promise

1. 若一个函数返回promise对象，就可以使用链式操作（then,catch）。
2. `then() 函数会返回一个全新的 Promise`，和原来的不同.
3. `如果想要在回调中获取上个 Promise 中的结果，上个 Promise 中必须要返回结果。(使用 () => x 比() => { return x; } 更简洁一点)`.
4. `一遇到异常抛出，promise链就会停下来，直接调用链式中的catch处理程序来继续当前执行`。

```js
doSomething()
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => console.log(`Got the final result: ${finalResult}`))
.catch(failureCallback);
```

### 9.1.2. 构建Promise

1. `构造函数主要是用来包装还未支持promises的函数`。

```javascript
//Promise构造函数执行时立即调用executor 函数
//resolve 和 reject 两个函数作为参数传递给executor（executor 函数在Promise构造函数返回所建promise实例对象前被调用）。
//resolve 和 reject 函数被调用时，分别将promise的状态改为fulfilled（完成）或rejected（失败）。
//executor 内部通常会执行一些异步操作，一旦异步操作执行完毕(可能成功/失败)，要么调用resolve函数来将promise状态改成fulfilled，要么调用reject 函数将promise的状态改为rejected。
//如果在executor函数中抛出一个错误，那么该promise 状态为rejected。executor函数的返回值被忽略。
var promise1 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve('foo');
  }, 300);
});

promise1.then(function(value) {
  console.log(value);
  // expected output: "foo"
});

console.log(promise1);
// expected output: [object Promise]
```

#### 9.1.2.1. Promise.all(iterable)

这个方法返回一个新的promise对象，该promise对象在`iterable参数对象里所有的promise对象都成功的时候`才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。这个新的promise对象在触发成功状态以后，会把一个包含iterable里`所有promise返回值的数组`作为成功回调的返回值，顺序跟iterable的顺序保持一致；如果这个新的promise对象触发了失败状态，它会把iterable里第一个触发失败的promise对象的错误信息作为它的失败错误信息。Promise.all方法常被用于处理多个promise对象的状态集合。

#### 9.1.2.2. Promise.race(iterable)

当iterable参数里的任意一个子promise被成功或失败后，父promise马上也会用子promise的成功返回值或失败详情作为参数调用父promise绑定的相应句柄，并返回该promise对象。

#### 9.1.2.3. Promise.reject(reason)

返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法

#### 9.1.2.4. Promise.resolve(value)

返回一个状态由给定value决定的Promise对象。如果该值是thenable(即，带有then方法的对象)，返回的Promise对象的最终状态由then方法执行决定；否则的话(该value为空，基本类型或者不带then方法的对象),返回的Promise对象状态为fulfilled，并且将该value传递给对应的then方法。通常而言，如果你不知道一个值是否是Promise对象，使用Promise.resolve(value) 来返回一个Promise对象,这样就能将该value以Promise对象形式使用。

## 9.2. Generator

1. 生成器对象是由一个 generator function 返回的,并且它符合可迭代协议和迭代器协议。

```js
function* gen() { 
  yield 1;
  yield 2;
  yield 3;
}

let g = gen(); 
// "Generator { }"
```
2. 异步任务的容器，生成器本质上是一种特殊的迭代器， Generator 执行后返回的是个指针对象，调用对象里的 next 函数，会移动内部指针，分阶段执行 Generator 函数 ，指向 yield 语句，返回一个对象 {value:当前的执行结果，done:是否结束}


### 9.2.1. 方法
#### 9.2.1.1. Generator.prototype.next()
返回一个由 yield表达式生成的值。
#### 9.2.1.2. Generator.prototype.return()
返回给定的值并结束生成器。
#### 9.2.1.3. Generator.prototype.throw()
向生成器抛出一个错误。

## 9.3. GeneratorFunction
## 9.4. AsyncFunction
## 9.5. Iterator
## 9.6. AsyncIterator
# 10. 反射

# 11. 国际化

# 12. WebAssembly

# 13. 其他

## 13.1. arguments

1. arguments 是一个对应于传递给函数的参数的类数组对象。
2. arguments对象是所有（非箭头）函数中都可用的局部变量。你可以使用arguments对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。
3. arguments对象不是一个 Array 。它类似于Array，但除了length属性和索引元素之外没有任何Array属性。例如，它没有 pop 方法。但是它可以被转换为一个真正的Array：
4. 在浏览器中箭头函数没有 arguments
5. 在 nodejs 中，有 arguments ，可通过其获取参数长度，但不能通过改对象获取参数列表

```js
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);
const args = [...arguments];
```


| var a = new Array()                             | 定义一个数组或者字典，定义字典最好使用 new Object                                                                                                                        |
| for (var key in dic){}                          | 遍历                                                                                                                                                                     |
| delete dic["r"];                                | 删除一个 KEY,使用 empty 替换，但是长度不变                                                                                                                               |
| delete dic.r                                    | 删除一个 KEY，使用 empty 替换，但是长度不变                                                                                                                              |
| `array.map()`                                   | 基本用法跟 forEach 方法类似，作用不难理解，“映射”嘛，也就是原数组被“映射”成`对应新数组`                                                                                  | `array.ma(element=>{return element.id})`                                                                                                                      |
| [].some()                                       | 返回一个 boolean，判断是否有元素是否符合 func 条件,返回 true 时终止遍历                                                                                                  |
| [].every()                                      | 返回一个 boolean，判断每个元素是否符合 func 条件,every 返回 false 时终止遍历,返回 true 继续遍历                                                                          |
| array.length                                    | 数组的长度　｜                                                                                                                                                           |
| arrayObject.concat(arrayX,arrayX,......,arrayX) | 返回一个新的数组,不改变原数组的值。该数组是通过把所有 arrayX 参数添加到 arrayObject 中生成的。如果要进行 concat() 操作的参数是数组，那么添加的是数组中的元素，而不是数组 |
| Array.from()                                    | 从一个类似数组或可迭代对象中创建一个新的数组实例                                                                                                                         | `console.log(Array.from('foo'));// expected output: Array ["f", "o", "o"]console.log(Array.from([1, 2, 3], x => x + x));// expected |output: Array [2, 4, 6]` |
| [...new Array(10).keys()]                       | 序列化一个新数组                                                                                                                                                         |
| indexOf()                                       | 判断是否存在                                                                                                                                                             | 不存在返回-1                                                                                                                                                  |

1. 判断 JS 对象是否拥有某属性两种方式，但稍有区别

```
1. in 运算符
var obj = {name:'jack'};
alert('name' in obj); // --> true
alert('toString' in obj); // --> true
可看到无论是name，还是原形链上的toString，都能检测到返回true。

2. hasOwnProperty 方法

var obj = {name:'jack'};
obj.hasOwnProperty('name'); // --> true
obj.hasOwnProperty('toString'); // --> false
原型链上继承过来的属性无法通过hasOwnProperty检测到，返回false。
需注意的是，虽然in能检测到原型链的属性，但for in通常却不行。
当然重写原型后for in在IE9/Firefox/Safari/Chrome/Opera下是可见的。见：for in的缺陷
```

2. js 扩展运算符（spread）是三个点（...）:`将一个数组转为用逗号分隔的参数序列`。

```
  array.push(...items);
  function add(x, y) {
    return x + y;
  }
  var numbers = [4, 38];
  add(...numbers) // 42
  //扩展运算符取代apply方法的一个实际的例子，应用Math.max方法，简化求出一个数组最大元素的写法。
  // ES5 的写法
  Math.max.apply(null, [14, 3, 77])
  // ES6 的写法
  Math.max(...[14, 3, 77])
  // 等同于
  Math.max(14, 3, 77);
  //通过push函数，将一个数组添加到另一个数组的尾部
  // ES5的 写法
  var arr1 = [0, 1, 2];
  var arr2 = [3, 4, 5];
  Array.prototype.push.apply(arr1, arr2);
  // ES6 的写法
  var arr1 = [0, 1, 2];
  var arr2 = [3, 4, 5];
  arr1.push(...arr2);
  //合并数组
  // ES5
  [1, 2].concat(more)
  // ES6
  [1, 2, ...more]
  var arr1 = ['a', 'b'];
  var arr2 = ['c'];
  var arr3 = ['d', 'e'];
  // ES5的合并数组
  arr1.concat(arr2, arr3);
  // [ 'a', 'b', 'c', 'd', 'e' ]
  // ES6的合并数组
  [...arr1, ...arr2, ...arr3]
  // [ 'a', 'b', 'c', 'd', 'e' ]

  扩展运算符将字符串转为真正的数组
  [...'hello']
  // [ "h", "e", "l", "l", "o" ]
```

### 13.1.1. 对象方法

#### 13.1.1.1. foreach

1. `array.forEach(function(currentValue, index, arr), thisValue)` 用于调用数组的每个元素，并将元素传递给回调函数,没有返回值，forEach 无法跳出循环（使用 return 也不行，只能抛异常跳出）。
2. `foreach`为同步执行，回调函数中不能使用`await`语法。
3. `foreach`放在`async`函数中，如果回调函数中有异步执行，则异步执行。防止异步执行可使用`for（const element of array）`在回调函数中使用`await`。

#### 13.1.1.2. includes 不会改变原数组

1. 判断当前数组是否包含某指定的值，如果是返回 true，否则返回 false。

#### 13.1.1.3. splice 此方法会改变原数组

splice() 方法通过删除或替换现有元素或者原地添加新的元素来修改数组,并以数组形式返回被修改的内容。

```
var array=[0,1,2,3,4,5];
1、向数组中的指定位置添加数据
arr.splice(2,0,"10")
输出: array=[0,1,10,2,3,4,5];
2、删除数组中的指定位置的数据并用新数据替换
arr.splice(2,1,"10")
输出: array=[0,1,10,3,4,5];
3、删除数组中的指定位置开始的多个数据并用新数据替换
arr.splice(2,3,"10")
输出: array=[0,1,10,5];
4、删除数组中的指定位置的数据方法
获取数组中数据的下标
var index = array.indexOf(4);
删除数据
if(index>-1){
array.splice(index,1);
}

```

## 13.2. 字符串

| 语句                      | 功能                                |
| ------------------------- | ----------------------------------- |
| str.split()               | 字符串转字符串数组                  |
| ['1','2','3'].map(Number) | 数字字符串数组转数字数组            |
| parseInt(str)             | 字符串转整数                        |
| parseFloat(str)           | 字符串转浮点数                      |
| string.indexOf('str')     | 判断字符串是否包含另一个字符串      |
| encodeURIComponent(str)   | 函数可把字符串作为 URI 组件进行编码 |

## 13.3. json

| 语句                | 功能        | 备注                               |
| ------------------- | ----------- | ---------------------------------- |
| JSON.stringify(arr) | 字典转 json | 注：arr 必须是 Object 不能是 Array |
| JSON.parse(jsonStr) | json 转数组 |

## 13.4. 转换

## 13.5. buffer(NodeJs)

1. 在 Node.js 中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区.
2. 一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。

### 13.5.1. 创建 Buffer 类

Buffer 提供了以下 API 来创建 Buffer 类：

```
Buffer.alloc(size[, fill[, encoding]])： 返回一个指定大小的 Buffer 实例，如果没有设置 fill，则默认填满 0
Buffer.allocUnsafe(size)： 返回一个指定大小的 Buffer 实例，但是它不会被初始化，所以它可能包含敏感的数据
Buffer.allocUnsafeSlow(size)
Buffer.from(array)： 返回一个被 array 的值初始化的新的 Buffer 实例（传入的 array 的元素只能是数字，不然就会自动被 0 覆盖）
Buffer.from(arrayBuffer[, byteOffset[, length]])： 返回一个新建的与给定的 ArrayBuffer 共享同一内存的 Buffer。
Buffer.from(buffer)： 复制传入的 Buffer 实例的数据，并返回一个新的 Buffer 实例
Buffer.from(string[, encoding])： 返回一个被 string 的值初始化的新的 Buffer 实例
// 创建一个长度为 10、且用 0 填充的 Buffer。
const buf1 = Buffer.alloc(10);

// 创建一个长度为 10、且用 0x1 填充的 Buffer。
const buf2 = Buffer.alloc(10, 1);

// 创建一个长度为 10、且未初始化的 Buffer。
// 这个方法比调用 Buffer.alloc() 更快，
// 但返回的 Buffer 实例可能包含旧数据，
// 因此需要使用 fill() 或 write() 重写。
const buf3 = Buffer.allocUnsafe(10);

// 创建一个包含 [0x1, 0x2, 0x3] 的 Buffer。
const buf4 = Buffer.from([1, 2, 3]);

// 创建一个包含 UTF-8 字节 [0x74, 0xc3, 0xa9, 0x73, 0x74] 的 Buffer。
const buf5 = Buffer.from('tést');

// 创建一个包含 Latin-1 字节 [0x74, 0xe9, 0x73, 0x74] 的 Buffer。
const buf6 = Buffer.from('tést', 'latin1');
```

## 13.6. Date

1. 创建`var myDate=new Date()`；自动把当前日期和时间保存为其初始值。
2. `Date`对象直接`/1000`获得时间戳。

## 13.7. NaN 和 Number.Nan 值一样

1. 表示不是一个数字（Not a Number）；
2. 编码中很少直接使用到 NaN。通常都是在计算失败时，作为 Math 的某个方法的返回值出现的（例如：Math.sqrt(-1)）或者尝试将一个字符串解析成数字但失败了的时候（例如：parseInt("blabla")）；
3. 等号运算符（== 和 ===） 不能被用来判断一个值是否是 NaN。必须使用 Number.isNaN() 或 isNaN() 函数。

## 13.8. 运算符

### 13.8.1. 位运算符

1. 位运算符工作于 32 位的数字上。任何数字操作都将转换为 32 位。结果会转换为 JavaScript 数字。

| 运算符 | 描述 | 例子       | 类似于      | 结果 | 十进制 |
| ------ | ---- | ---------- | ----------- | ---- | ------ |
| &      | AND  | x = 5 & 1  | 0101 & 0001 | 0001 | 1      |
|        |      | OR         | x = 5       | 1    | 0101   | 0001 | 0101 | 5 |
| ~      | 取反 | x = ~ 5    | ~0101       | 1010 | -6     |
| ^      | 异或 | x = 5 ^ 1  | 0101 ^ 0001 | 0100 | 4      |
| <<     | 左移 | x = 5 << 1 | 0101 << 1   | 1010 | 10     |
| >>     | 右移 | x = 5 >> 1 | 0101 >> 1   | 0010 | 2      |

