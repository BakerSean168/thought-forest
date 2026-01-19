---
tags:
  - tech/dev/javascript
  - type/concept
  - status/evergreen
description: ECMAScript/JavaScript 核心语法规范
created: 2025-10-16T16:58:17
updated: 2025-12-07T17:00:00
---

# ECMAScript 

**上级索引**：[[ECMAScript MOC]]

*ECMA（欧洲计算机制造商协会，European Computer Manufacturers Association）*

ECMAScript 是 JavaScript 的标准规范，定义了语言的语法、类型、语句、关键字、保留字、操作符、对象等。下面是一些核心知识点补充：

## 基础概念

- ECMAScript 是由 ECMA 国际（欧洲计算机制造商协会）制定的脚本语言标准，标准编号为 ECMA-262。
- JavaScript、JScript、ActionScript 都是 ECMAScript 的实现。
- ECMAScript 版本：ES3、ES5、ES6（ES2015）、ES7（ES2016）、ES8（ES2017）、ES9（ES2018）、ES10（ES2019）、ES11（ES2020）等，每年更新。
- [[ECMAScript语法基础]]（变量、数据类型、操作符、逻辑语句）
- [[ECMAScript中的执行上下文]] （执行上下文、作用域、函数作用域的规则和修改（apply等函数））
- [[ESMAScript内存管理]]（垃圾回收机制）
- [[ECMAScript基本引用类型]]（Date、原始值等）
- [[ECMAScript集合引用类型]]（Array、Map等）
- [[ECMAScript迭代器和生成器]]
- [[ECMAScript的面向对象]]
- [[ECMAScript模块模式]]（循环依赖、导入的值和原模块值的关系）
- [[ECMAScript闭包]]
- [[ECMAScript正则表达式]]
- [[ECMAScript原型]]
- [[ECMAScript类型检测方法]]
- [[ECMAScript类型转化]]

## 函数

- 函数声明与表达式
- 箭头函数（ES6）：简化函数写法，不绑定自己的 this
- 参数默认值、剩余参数（...args）、展开运算符（...arr）

## 对象与类

- 对象字面量增强（ES6）：属性简写、方法简写、计算属性名
- 原型链与继承
- 类（class，ES6）：构造函数、继承（extends）、静态方法、getter/setter

## 模块化

- ES6 模块：import/export
- CommonJS、AMD、UMD 是其他模块规范

## 异步与 Promise

- 回调函数
- Promise（ES6）：解决回调地狱
- async/await（ES8）：更优雅的异步写法

## 其他重要特性

- 解构赋值（ES6）：数组、对象解构
- 模板字符串（ES6）：反引号包裹，支持变量嵌入
- Symbol：唯一值标识符
- Set/Map（ES6）：新的集合类型
- Proxy/Reflect（ES6）：元编程能力
- Generator（ES6）：生成器函数，支持迭代和异步流程控制

## 参考资料

- [MDN ECMAScript](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Language_Resources)
- [ECMA-262 官方文档](https://www.ecma-international.org/publications-and-standards/standards/ecma-262/)
- [阮一峰 ECMAScript 6 入门](https://es6.ruanyifeng.com/)
