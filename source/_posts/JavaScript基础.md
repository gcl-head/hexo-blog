---
title: JavaScript基础
date: 2020-05-15 10:24:44
updated: 2020-05-15 10:24:44
tags:
    - javascript
categories:
---
## 数据类型和判断

### 分类

8种数据类型：

- number
- string
- boolean
- undefined
- null
- Symbol
- BigInt
- Object

其中Object类型又包括：

- Array
- Function
- Date
- RegExp

### 判断

- typeof

​	typeof返回一个表示数据类型的字符串，返回结果包括：number、string、boolean、object、undefined、function。typeof可以对基本类型number、string  、boolean、undefined做出准确的判断；而对于引用类型，除了function之外返回的都是object。

- instanceof

  专门用于实例和构造函数对应。

  ```javascript
  function Obj(value) {
    this.value = value;
  }
  let obj = new Obj("test");
  console.log(obj instanceof Obj); // output: true
  ```

- Array.isArray()：ES6 新增，用来判断是否是'Array'。`Array.isArray({})`返回`false`。

## 类型转换

- 显式强制类型转换

  ```js
  Number('123') // 123
  Number(true) //1
  String(123) //'123'
  123.toString //'123'
  String(true) //'true'
  Boolean(123) //true
  ```

- 显式解析数字字符串

  ```js
  parseInt('42') //42
  parseInt('42rem') //42
  Number('42rem') //NaN
  ```

- 隐式强制类型转换

  ```js
  123 + '' // '123'
  '123' - 0 // 123
  [1, 2] + [3, 4]   //"1,23,4" 因为数组的valueOf()操作无法得到简单基本类型值，于是调用toString()，因此两个数组变成了"1,2"和"3,4"，+将它们拼接后返回。
  ```

## 深拷贝和浅拷贝

​	在 JS 中，函数和对象都是浅拷贝（地址引用）；其他的，例如布尔值、数字等基础数据类型都是深拷贝（值引用）。

​	一般情况下,使用`JSON.parse(JSON.stringify(data))`进行深拷贝较为实用。即将对象转化为JSON字符串以字符串形势进行值引用后再转化回对象。但该方法对于复杂的对象类型，例如携带方法的对象无法完成拷贝。另外对于字符串如果转化为JSON，会在原有基础上增加一层双引号。

​	值得提醒的是，ES6 的`Object.assign()`和 ES7 的`...`解构运算符都是“浅拷贝”。实现深拷贝还是需要自己手动撸“轮子”或者借助第三方库（例如`lodash`）

深拷贝实现：

```javascript
/**
 * 数组的深拷贝函数
 * @param {Array} src
 * @param {Array} target
 */
function cloneArr(src, target) {
  for (let item of src) {
    if (Array.isArray(item)) {
      target.push(cloneArr(item, []));
    } else if (typeof item === "object") {
      target.push(deepClone(item, {}));
    } else {
      target.push(item);
    }
  }
  return target;
}

/**
 * 对象的深拷贝实现
 * @param {Object} src
 * @param {Object} target
 * @return {Object}
 */
function deepClone(src, target) {
  const keys = Reflect.ownKeys(src);
  let value = null;

  for (let key of keys) {
    value = src[key];

    if (Array.isArray(value)) {
      target[key] = cloneArr(value, []);
    } else if (typeof value === "object") {
      // 如果是对象而且不是数组, 那么递归调用深拷贝
      target[key] = deepClone(value, {});
    } else {
      target[key] = value;
    }
  }

  return target;
}
```

只需要调用deepClone即可实现各种类型的深拷贝。

## 事件流

当元素互相嵌套时，事件从子元素向父元素方向传递就叫做事件冒泡；

反之则称为事件捕获；

`addEventListener`给出了第三个参数同时支持冒泡与捕获：默认是`false`，事件冒泡；设置为`true`时，是事件捕获。

```html
<div id="app" style="width: 100vw; background: red;">
  <span id="btn">点我</span>
</div>
<script>
  // 事件捕获：先输出 "外层click事件触发"; 再输出 "内层click事件触发"
  var useCapture = true;
  var btn = document.getElementById("btn");
  btn.addEventListener(
    "click",
    function() {
      console.log("内层click事件触发");
    },
    useCapture
  );

  var app = document.getElementById("app");
  app.onclick = function() {
    console.log("外层click事件触发");
  };
</script>
```

## 常用的高阶函数

​	高阶函数是对其他函数进行操作的函数，操作可以是将它们作为参数，或者是返回它们。 简单来说，高阶函数是一个接收函数作为参数或将函数作为输出返回的函数。

- map()

  **返回**一个由原数组中的每个元素执行callback函数后的返回值组成的**新数组**。

  ```js
  var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
  var results = arr.map(function (x) {
    return x*x
  }) // [1, 4, 9, 16, 25, 36, 49, 64, 81]
  console.log(results)
  ```

- filter()

  数组过滤, 对原数组每个元素执行回调函数保留返回值为true的元素。

  ```js
  var arr = [1, 2, 4, 5, 6, 9, 10, 15]
  var re = arr.filter(function (x) {
      return x % 2 !== 0
  })
  console.log(re)
  ```

- reduce()

  以回调函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。

  `array.reduce(function(total, currentValue, currentIndex, arr), initialValue)`

  ```js
  var arr = [1, 3, 5, 7, 9];
  var re = arr.reduce(function (x, y) {
      return x + y;
  }); // 25
  console.log(re)
  ```

- sort()

  排序函数，默认排序根据ASCII码大小,因此会出现10排在2前面的情况，可以通过传入方法自定义sort()的排序方法
  
  ```js
  var arr = [10, 20, 1, 2];
  arr.sort(function (x, y) {
      if (x < y) {
          return -1;
      }
      if (x > y) {
          return 1;
      }
      return 0;
  });
  console.log(arr); // [1, 2, 10, 20]
  ```
  
- some()

  检索数组，对原数组的每个元素执行回调函数，如果有true则立刻返回true。

- every()

  检索数组，对原数组的每个元素执行回调函数，如果所有元素都返回true则返回true。

- find()

  检索数组，对原数组的每个元素执行回调函数，如果找到元素返回true，立刻返回该元素，否则返回undefined



## 闭包

通常在js中函数外部是无法访问函数内部的变量的，比如:

```js
function test () {
	let a = 1
}
console.log(a) // ReferenceError: Can't find variable: a
```

但使用闭包就可以实现，即通过在函数中定义返回函数内部变量的方法:

```js
function test () {
	let a = 1
  function cout () {
    console.log(a)
  }
  return cout
}
let b = test()
b()
```

这么看感觉闭包好像并没有什么作用，但闭包在面向对象编程中还是很有用的。比方说，他可以将函数与其所操作的某些数据（环境）关联起来：

```js
function makeSizer(size) {
  return function() {
    document.body.style.fontSize = size + 'px'
  };
}

var size12 = makeSizer(12)
var size14 = makeSizer(14)
var size16 = makeSizer(16)
```

比如上述代码，分别表示将字号调整为12、14和16px。

同理，他也可以用来模仿js中本不具备的私有方法。

**需要注意的是**，如果不是某些特定任务需要使用闭包，在其它函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能具有负面影响。

比如：

```js
function MyObject(name, message) {
  this.name = name.toString()
  this.message = message.toString()
  this.getName = function() {
    return this.name
  };

  this.getMessage = function() {
    return this.message
  };
}
```

上述代码，方法被重新赋值浪费了资源，却并没有利用到闭包的好处，因此建议改成如下：

```js
function MyObject(name, message) {
  this.name = name.toString()
  this.message = message.toString()
}
MyObject.prototype.getName = function() {
  return this.name
};
MyObject.prototype.getMessage = function() {
  return this.message
};
```