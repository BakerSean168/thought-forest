---
tags:
  - tech/lang/typescript
  - type/concept
  - status/growing
description: TypeScript基础知识
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
 moc/parent: "TypeScript MOC"
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---

# TS

## 简单知识

### 类型

基础类型
- string
- number
- boolean
- null
- undefined
- symbol
- bigint

复杂类型
- array 数组
- tuple 元组
- enum 枚举
- object  
    表示非原始类型的值，如对象、数组等  

特殊类型  
- any
- unknown
- never
- void

*还有联合类型（|）和交叉类型（&）*  
- 联合类型通过|符号连接多个类型从而生成新的类型。它主要是取多个类型的交集，即多个类型共有的类型才是联合类型最终的类型。  

## d.ts 文件

类型声明文件

## 面试

### TypeScript 中 type 和 interface 的区别

定义类型的两种核心工具

interface  
- 声明对象的形状（面向对象范式）  
- 仅能描述对象/函数类型  
- 以继承机制扩展  
- 性能更好（适合高频类型检查）

type  
- 类型别名（函数式编程思维）  
- 可描述任意类型（联合、元组等）  
- 强调灵活性，适合复杂类型操作  
- 以交叉类型（&）扩展

