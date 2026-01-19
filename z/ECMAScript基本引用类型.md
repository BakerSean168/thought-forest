---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript基本引用类型(Date/RegExp/原始值包装)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript基本引用类型 

## 基础概念


引用值（或者对象）是某个特定引用类型的实例。  
对象被认为是某个特定引用类型的实例。

有 Date、原始值包装类型、单例内置对象等

### 原始值包装类型

为了方便操作原始值，ECMAScript 提供了 3 种特殊的引用类型：Boolean、Number 和 String。

每当用到某个原始值的方法或属性时，后台都会创建一个相应原始包装类型的对象，从而暴露出操作原始值的各种方法。

Number、Bollean 、String

#### String

字符串的原型上暴露了一个@@iterator 方法，表示可以迭代字符串的每个字符。  
有了这个迭代器之后，字符串就可以通过解构操作符来解构了。

```js
let message = "abcde";
console.log([...message]);
```

### 单例内置对象

ECMA-262 对内置对象的定义是“任何由 ECMAScript 实现提供、与宿主环境无关，并在 ECMAScript 程序开始执行时就存在的对象”。  
这就意味着，开发者不用显式地实例化内置对象，因为它们已经实例化好了。  
前面我们已经接触了大部分内置对象，包括 Object、Array 和 String。  
本节介绍 ECMA-262 定义的另外两个单例内置对象：Global 和 Math。

#### Global

为一种兜底对象，它所针对的是不属于任何对象的属性和方法。

- URL 编码方法
- eval（）方法
- Global 对象属性
- window 对象

#### Math

提供了 Math 对象作为保存数学公式、信息和计算的地方。

## 使用指南

## 实战经验

## 经验总结

## 信息参考
