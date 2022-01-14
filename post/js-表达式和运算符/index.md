# JS-表达式和运算符


## 展开语法

1. 在函数调用时使用展开语法。

```js
function myFunction(x, y, z) {}
var args = [0, 1, 2];
myFunction(...args);
```

2. 在 new 表达式中应用

```js
var dateFields = [1970, 0, 1]; // 1970年1月1日
var d = new Date(...dateFields);
```

3. 构造字面量数组时使用展开语法

```js
var parts = ["shoulders", "knees"];
var lyrics = ["head", ...parts, "and", "toes"];
// ["head", "shoulders", "knees", "and", "toes"]
```

4. 数组拷贝(执行的都是浅拷贝)

```
var arr = [1, 2, 3];
var arr2 = [...arr]; // like arr.slice()
arr2.push(4);
```

5. 连接多个数组

```js
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
var arr3 = [...arr1, ...arr2];
```

6. 构造字面量对象时使用展开语法

```js
var obj1 = { foo: "bar", x: 42 };
var obj2 = { foo: "baz", y: 13 };

var clonedObj = { ...obj1 };
// 克隆后的对象: { foo: "bar", x: 42 }

var mergedObj = { ...obj1, ...obj2 };
// 合并后的对象: { foo: "baz", x: 42, y: 13 }

var obj1 = { foo: "bar", x: 42 };
var obj2 = { foo: "baz", y: 13 };
const merge = (...objects) => ({ ...objects });

var mergedObj = merge(obj1, obj2);
// Object { 0: { foo: 'bar', x: 42 }, 1: { foo: 'baz', y: 13 } }

var mergedObj = merge({}, obj1, obj2);
// Object { 0: {}, 1: { foo: 'bar', x: 42 }, 2: { foo: 'baz', y: 13 } }
```

7. 只能用于可迭代对象(比如 Set)

