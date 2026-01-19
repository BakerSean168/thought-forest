---
tags:
  - tech/dev/programming
  - type/concept
  - status/growing
description: ECMAScript函数概念与用法概览
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[ECMAScript]]

---


# ECMAScript函数 

**上级索引**：[[ECMAScript MOC]] | [[ECMAScript]]

## 基础概念

## 函数

函数实际上是对象。  
每个函数都是 Function 类型的实例，而 Function 也有属性和方法，跟其他引用类型一样。因为函数是对象，所以函数名就是指向函数对象的指针，而且不一定与函数本身紧密绑定。

创建（实例化）函数的方式：

- 声明式
- 函数表达式
- 箭头函数
- new Function

_把函数想象为对象，把函数名想象为指针是很重要的。_

### 箭头函数

箭头函数不能使用 arguments、super 和 new.target，也不能用作构造函数。此外，箭头函数也没有 prototype 属性。  
箭头函数没有自己的 this，它的 this 继承自定义时的外层作用域（词法作用域），且无法通过 call、apply 或 bind 改变。而构造函数需要通过 new 操作符将 this 绑定到新创建的实例对象上。  
箭头函数的定位是轻量级函数表达式，适用于需要固定 this 的场景（如回调函数），而非面向对象的构造函数。ES6 通过限制其构造函数能力，避免误用。

### 函数名

函数名就是指向函数的指针，所以它们跟其他包含对象指针的变量具有相同的行为。这意味着一个函数可以有多个名称。
ECMAScript 6 的所有函数对象都会暴露一个只读的 name 属性，其中包含关于函数的信息。

### 参数

ECMAScript 函数既不关心传入的参数个数，也不关心这些参数的数据类型。

因为 ECMAScript 函数的参数在内部表现为一个数组。函数被调用时总会接收一个数组，但函数并不关心这个数组中包含什么。如果数组中什么也没有，那没问题；如果数组的元素超出了要求，那也没问题。  
事实上，在使用 function 关键字定义（非箭头）函数时，可以在函数内部访问 **arguments 对象**，从中取得传进来的每个参数值。

- arguments 对象可以跟命名参数一起使用
- arguments 对象的值始终会与对应的命名参数同步。
- 为 arguments 对象的长度是根据传入的参数个数，而非定义函数时给出的命名参数个数确定的。

#### 箭头函数的参数

如果函数是使用箭头语法定义的，那么传给函数的参数将不能使用 arguments 关键字访问，而只
能通过定义的命名参数访问。  
但可以在包装函数中把它提供给箭头函数。

### 没有重载

ECMAScript 函数不能像传统编程那样重载。在其他语言比如 Java 中，一个函数可以有两个定义，只要签名（接收参数的类型和数量）不同就行。如前所述，ECMAScript 函数没有签名，因为参数是由包含零个或多个值的数组表示的。没有函数签名，自然也就没有重载。

### 默认参数值

```js
function makeKing(name = "Henry") {
  return `King ${name} VIII`;
}
console.log(makeKing("Louis")); // 'King Louis VIII'
console.log(makeKing()); // 'King Henry VIII'
```

- 在使用默认参数时，arguments 对象的值不反映参数的默认值，只反映传给函数的参数。
- 默认参数值并不限于原始值或对象类型，也可以使用调用函数返回的值

**默认参数作用域与暂时性死区**

- 因为在求值默认参数时可以定义对象，也可以动态调用函数，所以函数参数肯定是在某个作用域中求值的。
- 因为参数是按顺序初始化的，所以后定义默认值的参数可以引用先定义的参数。
- 参数初始化顺序遵循“暂时性死区”规则，即前面定义的参数不能引用后面定义的。

### 参数扩展与收集

#### 扩展参数

console.log(getSum(...values)); // 10

#### 收集参数

```js
function getSum(...values) {
  // 顺序累加values 中的所有值
  // 初始值的总和为0
  return values.reduce((x, y) => x + y, 0);
}
```

收集参数的前面如果还有命名参数，则只会收集其余的参数；如果没有则会得到空数组。因为收集参数的结果可变，所以只能把它作为最后一个参数

```js
// 不可以
function getProduct(...values, lastValue) {}
// 可以
function ignoreFirst(firstValue, ...values) {
  console.log(values);
}
ignoreFirst();       // []
ignoreFirst(1);      // []
ignoreFirst(1,2);    // [2]
ignoreFirst(1,2,3);  // [2, 3]
```

### 函数声明与函数表达式

- 函数声明会在任何代码执行之前先被读取并添加到执行上下文。这个过程叫作函数声明提升（function declaration hoisting）。
- 函数表达式必须等到代码执行到它那一行，才会在执行上下文中生成函数定义。

### 函数作为值

因为函数名在 ECMAScript 中就是变量，所以函数可以用在任何可以使用变量的地方。这意味着不仅可以把函数作为参数传给另一个函数，而且还可以在一个函数中返回另一个函数。

```js
function callSomeFunction(someFunction, someArgument) {
  return someFunction(someArgument);
}
```

函数返回函数

### 函数内部

在 ECMAScript 5 中，函数内部存在两个特殊的对象：arguments 和 this。ECMAScript 6 又新增了 new.target 属性。

#### arguments

- 使用 arguments.callee 就可以让函数逻辑与函数名解耦

#### this

- 在标准函数中，this 引用的是把函数当成方法调用的上下文对象，这时候通常称其为 this 值（在网页的全局上下文中调用函数时，this 指向 windows）。
- 在事件回调或定时回调中调用某个函数时，this 值指向的并非想要的对象。此时将回调函数写成箭头函数就可以解决问题。这是因为箭头函数中的 this 会保留定义该函数时的上下文

```js
function King() {
  this.royaltyName = "Henry";
  // this 引用 King 的实例
  setTimeout(() => console.log(this.royaltyName), 1000);
}

function Queen() {
  this.royaltyName = "Elizabeth";
  // this 引用 window 对象
  setTimeout(function () {
    console.log(this.royaltyName);
  }, 1000);
}
new King(); // Henry
new Queen(); // undefined
```

#### caller

ECMAScript 5 也会给函数对象上添加一个属性：caller。  
这个属性引用的是调用当前函数的函数，或者如果是在全局作用域中调用的则为 null。

#### new.target

ECMAScript 中的函数始终可以作为构造函数实例化一个新对象，也可以作为普通函数被调用。  
ECMAScript 6 新增了检测函数是否使用 new 关键字调用的 new.target 属性。  
如果函数是正常调用的，则 new.target 的值是 undefined；如果是使用 new 关键字调用的，则 new.target 将引用被调用的构造函数。

```js
function King() {
  if (!new.target) {
    throw 'King must be instantiated using "new"';
  }
  console.log('King instantiated using "new"');
}
new King(); // King instantiated using "new"
King(); // Error: King must be instantiated using "new"
```

#### 函数属性和方法

每个函数都有两个属性：length 和 prototype。

- length 属性保存函数定义的命名参数的个数
- prototype 是保存引用类型所有实例方法的地方，这意味着 toString()、valueOf()等方法实际上都保存在 prototype 上，进而由所有实例共享。

函数还有两个方法：apply()和 call()。
这两个方法都会以指定的 this 值来调用函数，即会设置调用函数时函数体内 this 对象的值。

#### 函数表达式

函数表达式看起来就像一个普通的变量定义和赋值，即创建一个函数再把它赋值给一个变量 functionName。这样创建的函数叫作匿名函数（anonymous funtion），因为 function 关键字后面没有标识符。（匿名函数有也时候也被称为兰姆达函数）。未赋值给其他变量的匿名函数的 name 属性是空字符串。

### 递归

### 尾调用优化

ECMAScript 6 规范新增了一项内存管理优化机制，让 JavaScript 引擎在满足条件时可以重用栈帧。  
具体来说，这项优化非常适合“尾调用”，即外部函数的返回值是一个内部函数的返回值。

### 闭包

- 闭包是指有权访问另一个函数作用域中变量的函数。
- 闭包通常用来创建内部变量，使得 这些变量不能被外部随意修改，同时又可以通过指定的接口来操作。
- 由于闭包会保持对外部作用域的引用，所以它的内存不会被自动回收，容易导致内存泄漏。

优点：

- 保护变量
- 延长变量生命周期
- 实现模块化
- 保持状态

缺点：

- 内存占用
- 性能损耗  
  涉及作用域链查找过程，会带来一定性能损耗。

特性：

- 函数嵌套  
  闭包的实现依赖于函数嵌套，即在一个函数内部定义另一个函数。
- 记忆外部变量
- 延长作用域链
- 返回函数

#### 应用

- 自执行函数
- 防抖
- 节流
- 函数柯里化  
  函数柯里化（Currying）是一种将多个参数的函数转换为一系列接受单个参数的函数的过程。
- 链式调用
- 迭代器
- 发布-订阅模式

#### 问题

闭包造成的内存泄漏怎么解决？  
 闭包中的内存泄漏指的是在闭包函数中，由于对外部变量的引用而导致这些变量无法被垃圾回收机制释放的情况。当一个函数内部定义了一个闭包，并且这个闭包引用了外部变量时，如果这个闭包被其他地方持有，就会导致外部变量无法被正常释放，从而造成内存泄漏。  
 解决闭包中的内存泄漏问题通常需要注意解除外部变量和闭包函数的引用，以及解绑函数本身的引用，使得闭包中引用的外部变量和闭包函数能够被垃圾回收机制释放。

```js

function createClosure() {
  let value = 'Hello';
​
  // 闭包函数
  var closure = function() {
    console.log(value);
  };
​
  // 解绑定闭包函数，并释放资源
  var releaseClosure = function() {
    value = null; // 解除外部变量的引用
    closure = null; // 解除闭包函数的引用
    releaseClosure = null; // 解除解绑函数的引用
  };
​
  // 返回闭包函数和解绑函数
  return {
    closure,
    releaseClosure
  };
}
​
// 创建闭包
var closureObj = createClosure();
​
// 调用闭包函数
closureObj.closure(); // 输出：Hello
​
// 解绑闭包并释放资源
closureObj.releaseClosure();
​
// 尝试调用闭包函数，此时已解绑，不再引用外部变量
closureObj.closure(); // 输出：null

```

#### 防抖

防抖的核心是 延迟执行：高频事件触发后，若在指定时间间隔内再次触发，则重置计时器；只有最后一次触发后的等待时间结束，才会执行目标函数。

```js
// 基础版本
function debounce(func, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

//支持立即执行版本
function debounce(func, delay, immediate = false) {
  let timer;
  return function (...args) {
    const callNow = immediate && !timer;
    clearTimeout(timer);
    timer = setTimeout(() => {
      if (!immediate) func.apply(this, args);
      timer = null;
    }, delay);
    if (callNow) func.apply(this, args);
  };
}
```

应用场景：

- 搜索框输入联想（用户停止输入后发送请求）
- 窗口大小调整后重新布局
- 表单验证（停止输入后统一校验）

#### 节流

节流的本质是 频率限制：无论事件触发多频繁，在固定时间间隔内最多执行一次目标函数。

```js
// 时间戳版（立即执行）
function throttle(func, limit) {
  let last = 0;
  return function (...args) {
    const now = Date.now();
    if (now - last >= limit) {
      func.apply(this, args);
      last = now;
    }
  };
}

// 定时器版（延迟执行）
function throttle(func, limit) {
  let timer;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        func.apply(this, args);
        timer = null;
      }, limit);
    }
  };
}

// 优化版本（结合首次和最后一次执行）
function throttle(func, limit, { leading = true, trailing = true }) {
  let last = 0,
    timer;
  return function (...args) {
    const now = Date.now();
    if (leading && now - last >= limit) {
      func.apply(this, args);
      last = now;
    } else if (trailing && !timer) {
      timer = setTimeout(() => {
        if (trailing) func.apply(this, args);
        timer = null;
        last = now;
      }, limit - (now - last));
    }
  };
}
```

### 立即调用的函数表达式

立即调用的匿名函数又被称作立即调用的函数表达式（IIFE，Immediately Invoked Function Expression）。  
它类似于函数声明，但由于被包含在括号中，所以会被解释为函数表达式。紧跟在第一组括号后面的第二组括号会立即调用前面的函数表达式。

### 私有变量

严格来讲，JavaScript 没有私有成员的概念，所有对象属性都公有的。不过，倒是有私有变量的概念。任何定义在函数或块中的变量，都可以认为是私有的，因为在这个函数或块的外部无法访问其中的变量。私有变量包括函数参数、局部变量，以及函数内部定义的其他函数。

1. 静态私有变量
   特权方法也可以通过使用私有作用域定义私有变量和函数来实现。
2. 模块模式
   在一个单例对象上实现了相同的隔离和封装。单例对象（singleton）就是只有一个实例的对象。
3. 模块增强模式  
   在返回对象之前先对其进行增强。这适合单例对象需要是某个特定类型的实例，但又必须给它添加额外属性或方法的场景。


## 使用指南

## 实战经验

## 经验总结

## 信息参考

