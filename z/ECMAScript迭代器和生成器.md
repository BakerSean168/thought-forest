---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript迭代器(Iterator)与生成器(Generator)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript迭代器和生成器 

## 基础概念

### 迭代器

#### 迭代器模式

迭代器模式（特别是在 ECMAScript 这个语境下）描述了一个方案，即可以把有些结构称为“可迭代对象”（iterable），因为它们实现了正式的 Iterable 接口，而且可以通过迭代器 Iterator 消费。

- 任何实现 Iterable 接口的数据结构都可以被实现 Iterator 接口的结构“消费”（consume）。
- 迭代器（iterator）是按需创建的一次性对象。
- 每个迭代器都会关联一个可迭代对象，而迭代器会暴露迭代其关联可迭代对象的 API。
- 迭代器无须了解与其关联的可迭代对象的结构，只需要知道如何取得连续的值。这种概念上的分离正是 Iterable 和 Iterator 的强大之处。

#### 可迭代协议

实现 Iterable 接口（可迭代协议）要求同时具备两种能力：支持迭代的自我识别能力和创建实现 Iterator 接口的对象的能力。

在 ECMAScript 中，这意味着必须暴露一个属性作为“默认迭代器”，而且这个属性必须使用特殊的 Symbol.iterator 作为键。这个默认迭代器属性必须引用一个迭代器工厂函数，调用这个工厂函数必须返回一个新迭代器。

内置 Iterable 接口的类型：  
字符串、数组、映射、集合、arguments 对象、NodeList 等 DOM 集合类型

实际写代码过程中，不需要显式调用这个工厂函数来生成迭代器。实现可迭代协议的所有类型都会
自动兼容接收可迭代对象的任何语言特性。

#### 迭代器协议

迭代器是一种一次性使用的对象，用于迭代与其关联的可迭代对象。迭代器 API 使用 next()方法
在可迭代对象中遍历数据。每次成功调用 next()，都会返回一个 IteratorResult 对象，其中包含迭
代器返回的下一个值。若不调用 next()，则无法知道迭代器的当前位置。

- 每个迭代器都表示对可迭代对象的一次性有序遍历。不同迭代器的实例相互之间没有联系，只会独
  立地遍历可迭代对象
- 迭代器并不与可迭代对象某个时刻的快照绑定，而仅仅是使用游标来记录遍历可迭代对象的历程。

```js
// 自定义迭代器
class Counter {
  constructor(limit) {
    this.limit = limit;
  }
  [Symbol.iterator]() {
    let count = 1,
      limit = this.limit;
    return {
      next() {
        if (count <= limit) {
          return { done: false, value: count++ };
        } else {
          return { done: true, value: undefined };
        }
      },
    };
  }
}
```

#### 提前终止迭代器

可选的 return()方法用于指定在迭代器提前关闭时执行的逻辑。  
执行迭代的结构在想让迭代器知道它不想遍历到可迭代对象耗尽时，就可以“关闭”迭代器。可能的情况包括：

- for-of 循环通过 break、continue、return 或 throw 提前退出；
- 解构操作并未消费所有值。

return()方法必须返回一个有效的 IteratorResult 对象。简单情况下，可以只返回{ done: true }。

如果迭代器没有关闭，则还可以继续从上次离开的地方继续迭代。

### 生成器

生成器是 ECMAScript 6 新增的一个极为灵活的结构，拥有在一个函数块内暂停和恢复代码执行的能力。  
这种新能力具有深远的影响，比如，使用生成器可以自定义迭代器和实现协程。

#### 生成器基础

生成器的形式是一个函数，函数名称前面加一个星号（\*）表示它是一个生成器。只要是可以定义函数的地方，就可以定义生成器。

```js
// 生成器函数声明
function* generatorFn() {}
// 生成器函数表达式
let generatorFn = function* () {};
// 作为对象字面量方法的生成器函数
let foo = {
  *generatorFn() {},
};
// 作为类实例方法的生成器函数
class Foo {
  *generatorFn() {}
}
// 作为类静态方法的生成器函数
class Bar {
  static *generatorFn() {}
}
```

_箭头函数不能用来定义生成器函数_

#### 通过 yield 中断执行

yield 关键字可以让生成器停止和开始执行，也是生成器最有用的地方。生成器函数在遇到 yield
关键字之前会正常执行。遇到这个关键字后，执行会停止，函数作用域的状态会被保留。停止执行的生
成器函数只能通过在生成器对象上调用 next()方法来恢复执行

1. 生成器对象作为可迭代对象
2. 使用 yield 实现输入和输出
3. 产生可迭代对象  
   可以使用星号增强 yield 的行为，让它能够迭代一个可迭代对象，从而一次产出一个值  
   实际上只是将一个可迭代对象序列化为一连串可以单独产出的值
4. 使用 yield\*实现递归算法

```js
// 生成器作为默认迭代器
class Foo {
  constructor() {
    this.values = [1, 2, 3];
  }
  *[Symbol.iterator]() {
    yield* this.values;
  }
}
const f = new Foo();
for (const x of f) {
  console.log(x);
}
// 1
// 2
// 3
```

#### 提前终止生成器

与迭代器类似，生成器也支持“可关闭”的概念。一个实现 Iterator 接口的对象一定有 next()
方法，还有一个可选的 return()方法用于提前终止迭代器。生成器对象除了有这两个方法，还有第三
个方法：throw()。

与迭代器不同，所有生成器对象都有 return()方法，只要通过它进入关闭状态，就无法恢复了。  
后续调用 next()会显示 done: true 状态，而提供的任何返回值都不会被存储或传播。

## 使用指南

## 实战经验

## 经验总结

## 信息参考
