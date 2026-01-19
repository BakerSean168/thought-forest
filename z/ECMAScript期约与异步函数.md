---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript Promise期约与async/await异步编程
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript期约与异步函数 

## 基础概念

### 异步编程

#### 同步与异步

#### 以往的异步编程模式

### 期约

期约是对尚不存在结果的一个替身。

#### Promises/A+规范

早期的期约机制在 jQuery 和 Dojo 中是以 Deferred API 的形式出现的。到了 2010 年，CommonJS 项目实现的 Promises/A 规范日益流行起来。  
ECMAScript 6 增加了对 Promises/A+规范的完善支持，即 Promise 类型。一经推出，Promise 就大受欢迎，成为了主导性的异步编程机制。

#### 期约基础

ECMAScript 6 新增的引用类型 Promise，可以通过 new 操作符来实例化。创建新期约时需要传入执行器（executor）函数作为参数

1. 期约状态机  
   待定（pending）  
   兑现（fulfilled，或者 resolved）  
   拒绝（rejected）  
   待定（pending）是期约的最初始状态。在待定状态下，期约可以落定（settled）为代表成功的兑现（fulfilled）状态，或者代表失败的拒绝（rejected）状态。无论落定为哪种状态都是不可逆的。只要从待定转换为兑现或拒绝，期约的状态就不再改变。而且，也不能保证期约必然会脱离待定状态。因此，组织合理的代码无论期约解决（resolve）还是拒绝（reject），甚至永远处于待定（pending）状态，都应该具有恰当的行为。
2. 解决值、拒绝理由及期约用例
3. 通过执行函数控制期约状态
4. Promise.resolve()
5. Promise.reject()
6. 同步/异步执行的二元性

## 使用指南

## 实战经验

## 经验总结

## 信息参考
