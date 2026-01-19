---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript集合引用类型(Array/Map/Set/WeakMap)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript集合引用类型 

## 基础概念

## 集合引用类型

### Object

创建 Object 实例的方法：

1. new 操作符和 Object 构造函数
2. 对象字面量  
   属性名可以为数字（会自动转换为字符串）或字符串

#### Object 方法

| 方法名                          | 描述                                        | 示例                                                               |
| ------------------------------- | ------------------------------------------- | ------------------------------------------------------------------ |
| `Object.keys(obj)`              | 返回对象自身可枚举属性的键名数组            | `Object.keys({a:1, b:2})` → `["a", "b"]`                           |
| `Object.values(obj)`            | 返回对象自身可枚举属性的键值数组            | `Object.values({a:1, b:2})` → `[1, 2]`                             |
| `Object.entries(obj)`           | 返回对象自身可枚举属性的键值对二维数组      | `Object.entries({a:1, b:2})` → `[["a", 1], ["b", 2]]`              |
| `Object.assign(target, ...src)` | 合并源对象属性到目标对象（浅拷贝）          | `Object.assign({a:1}, {b:2})` → `{a:1, b:2}`                       |
| `Object.create(proto)`          | 创建以指定对象为原型的新对象                | `const obj = Object.create({name: "Alice"}); obj.name` → `"Alice"` |
| `Object.freeze(obj)`            | 冻结对象（不可修改/添加/删除属性）          | `Object.freeze(obj); obj.a = 2` → 修改失败                         |
| `Object.seal(obj)`              | 密封对象（不可添加/删除属性，但可修改值）   | `Object.seal(obj); obj.a = 2` → 修改有效                           |
| `Object.is(val1, val2)`         | 严格值比较（处理 `NaN` 和 `±0`）            | `Object.is(NaN, NaN)` → `true`；`Object.is(0, -0)` → `false`       |
| `Object.fromEntries(entries)`   | 将键值对列表转换为对象                      | `Object.fromEntries([["a",1], ["b",2]])` → `{a:1, b:2}`            |
| `Object.hasOwn(obj, prop)`      | 检查对象自身是否包含指定属性（ES2022 新增） | `Object.hasOwn({a:1}, 'a')` → `true`                               |

### [[JavaScript-Array | Array]]


### 定型数组

定型数组（typed array）是 ECMAScript 新增的结构，目的是提升向原生库传输数据的效率。  
实际上，JavaScript 并没有“TypedArray”类型，它所指的其实是一种特殊的包含数值类型的数组。

#### ArrayBuffer

可用于在内存中分配特定数量的字节空间

#### DataView

第一种允许你读写 ArrayBuffer 的视图

ElementType  
字节序  
边界情形

#### 定型数组

定型数组是另一种形式的 ArrayBuffer 视图。虽然概念上与 DataView 接近，但定型数组的区别在于，它特定于一种 ElementType 且遵循系统原生的字节序。相应地，定型数组提供了适用面更广的 API 和更高的性能。  
设计定型数组的目的就是提高与 WebGL 等原生库交换二进制数据的效率。  
由于定型数组的二进制表示对操作系统而言是一种容易使用的格式，JavaScript 引擎可以重度优化算术运算、按位运算和其他对定型数组的常见操作，因此使用它们速度极快。

### Map

与 Object 只能使用数值、字符串或符号作为键不同，Map 可以使用任何 JavaScript 数据类型作为键。

与 Object 类型的一个主要差异是，Map 实例会维护键值对的插入顺序，因此可以根据插入顺序执行迭代操作。

| 比较维度     | Object                                     | Map                                                | 选择建议                    |
| ------------ | ------------------------------------------ | -------------------------------------------------- | --------------------------- |
| **内存占用** | 随键数量线性增加                           | 随键数量线性增加，但相同内存可多存储约 50%的键值对 | 内存敏感场景选择 Map        |
| **插入性能** | 性能较好                                   | 所有浏览器中通常稍快，性能更佳                     | 大量插入操作选择 Map        |
| **查找速度** | 少量键值对时可能更快，支持数组式使用的优化 | 大型数据查找性能相当，不支持特殊优化               | 频繁查找少量数据选择 Object |
| **删除性能** | delete 操作性能较差                        | delete() 操作性能优于插入和查找                    | 频繁删除操作选择 Map        |

#### Map 常用方法

| 操作类型         | 方法/属性          | 语法示例/描述                                                              | 来源参考 |
| ---------------- | ------------------ | -------------------------------------------------------------------------- | -------- |
| **创建 Map**     | `new Map()`        | `const map = new Map();` 或 `new Map([[key1, val1], [key2, val2]])` 初始化 |          |
| **添加键值对**   | `.set(key, value)` | 支持链式调用：`map.set('a', 1).set('b', 2)`                                |          |
| **获取值**       | `.get(key)`        | `map.get('a')` → 返回对应值，不存在返回 `undefined`                        |          |
| **判断键存在**   | `.has(key)`        | `map.has('a')` → 返回布尔值                                                |          |
| **删除键值对**   | `.delete(key)`     | `map.delete('a')` → 成功删除返回 `true`，否则 `false`                      |          |
| **清空 Map**     | `.clear()`         | 删除所有键值对：`map.clear()`                                              |          |
| **获取大小**     | `.size`            | 属性直接访问：`map.size` → 返回键值对数量                                  |          |
| **遍历键**       | `.keys()`          | 返回迭代器：`for (let key of map.keys()) { ... }`                          |          |
| **遍历值**       | `.values()`        | 返回迭代器：`for (let val of map.values()) { ... }`                        |          |
| **遍历键值对**   | `.entries()`       | 返回 `[key, value]` 数组迭代器：`for (let [k,v] of map.entries()) { ... }` |          |
| **迭代全部元素** | `.forEach()`       | `map.forEach((value, key) => { ... })`（注意参数顺序为 `值, 键`）          |          |

### WeakMap

ECMAScript 6 新增的“弱映射”（WeakMap）是一种新的集合类型，为这门语言带来了增强的键/
值对存储机制。WeakMap 是 Map 的“兄弟”类型，其 API 也是 Map 的子集。WeakMap 中的“weak”（弱），
描述的是 JavaScript 垃圾回收程序对待“弱映射”中键的方式。

弱映射中的键只能是 Object 或者继承自 Object 的类型，尝试使用非对象设置键会抛出
TypeError。值的类型没有限制。

### Set

ECMAScript 6 新增的 Set 是一种新集合类型，为这门语言带来集合数据结构。Set 在很多方面都
像是加强的 Map，这是因为它们的大多数 API 和行为都是共有的。

Set 会维护值插入时的顺序，因此支持按顺序迭代。

### WeakSet

ECMAScript 6 新增的“弱集合”（WeakSet）是一种新的集合类型，为这门语言带来了集合数据结
构。WeakSet 是 Set 的“兄弟”类型，其 API 也是 Set 的子集。WeakSet 中的“weak”（弱），描述的
是 JavaScript 垃圾回收程序对待“弱集合”中值的方式。


