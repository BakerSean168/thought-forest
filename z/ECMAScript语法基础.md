---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript语法基础(变量/数据类型/操作符)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript语法基础 

## 基础概念

### 定义变量的三种方式

let
块级作用域  
 变量提升但未初始化  
 需要重新赋值的变量  
const
块级作用域  
 变量提升但未初始化  
 声明常量或引用类型，避免意外修改  
var
函数级作用域  
 提升初始化为 undefined  
 不推荐使用


### 原始值和引用值

ECMAScript 变量可以包含两种不同类型的数据：原始值和引用值。

- 原始值（primitive value）：  
  就是最简单的数据  
  6 种原始值：Undefined、Null、Boolean、Number、String 和 Symbol。
- 引用值（reference value）：  
  则是由多个值构成的对象。  
  引用值是保存在内存中的对象。

在把一个值赋给变量时，JavaScript 引擎必须确定这个值是原始值还是引用值。
JavaScript 不允许直接访问内存位置，因此也就不能直接操作对象所在的内存空间。

- 保存原始值的变量是**按值（by value）**访问
- 保存引用值的变量是**按引用（by reference）**访问

### 传递参数

ECMAScript 中所有函数的参数都是按值传递的。

### 数据类型

原始类型

- number：整数和小数（比如 1 和 3.14）
- string：文本（比如 Hello World）。
- boolean：表示真伪的两个特殊值，即 true（真）和 false（假）
- undefined：表示“未定义”或不存在，变量声明了，但没有赋值
- null：表示空值，即此处的值为空，空对象引用。
- symbol（ES6）：唯一且不可变的值，用于对象属性的唯一标识。
- bigint（ES11）：表示任意精度的大整数  
  特点
- 原始类型存储在 stack 中，值直接保存在变量访问的位置
- 复制的是值本身

引用类型

- 狭义的对象（object）
- 数组（array）
- 函数（function）
  特点
- 引用类型存储在 heap 中，变量保存的是实际对象的引用（指针）
- 复制的是引用，多个变量引用同一对象时，一个变量的修改会影响其他变量。


### 确定类型

#### typeof

检查变量类型，返回字符串，表示数据类型

```js
console.log(typeof undefined); // "undefined"
console.log(typeof true); // "boolean"
console.log(typeof 42); // "number"
console.log(typeof "hello"); // "string"
console.log(typeof {}); // "object"
console.log(typeof []); // "object"
console.log(typeof null); // "object" (特殊情况)
console.log(typeof function () {}); // "function"
console.log(typeof Symbol()); // "symbol"
console.log(typeof 10n); // "bigint"
```

主要用于基本数据类型、函数、未定义类型、symbol

### [[ESMAScript操作符]]

### [[ECMAScript语法基础]]



