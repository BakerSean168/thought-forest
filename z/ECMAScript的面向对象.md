---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript面向对象编程(OOP)与类
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript的面向对象 

## 基础概念

## 对象、类、面向对象编程

### 属性类型

ECMA-262 使用一些内部特性来描述属性的特征。这些特性是由为 JavaScript 实现引擎的规范定义
的。因此，开发者不能在 JavaScript 中直接访问这些特性。为了将某个特性标识为内部特性，规范会用
两个中括号把特性的名称括起来，比如[[Enumerable]]。

属性分两种：数据属性和访问器属性。

#### 数据属性

数据属性包含一个保存数据值的位置。  
值会从这个位置读取，也会写入到这个位置。  
数据属性有 4 个特性描述它们的行为。

- [[Configurable]]
- [[Enumerable]]
- [[Writable]]
- [[Value]]

要修改属性的默认特性，就必须使用 Object.defineProperty()方法。

#### 访问器属性

访问器属性不包含数据值。相反，它们包含一个获取（getter）函数和一个设置（setter）函数，不
过这两个函数不是必需的。  
在读取访问器属性时，会调用获取函数，这个函数的责任就是返回一个有效的值。  
在写入访问器属性时，会调用设置函数并传入新值，这个函数必须决定对数据做出什么修改。  
访问器属性有 4 个特性描述它们的行为。

- [[Configurable]]
- [[Enumerable]]
- [[Get]]
- [[Set]]

访问器属性是不能直接定义的，必须使用 Object.defineProperty()。

#### 定义多个属性

Object.defineProperties()

#### 读取属性的特性

用 Object.getOwnPropertyDescriptor()方法可以取得指定属性的属性描述符

### 创建对象

原型、继承、类

#### 工厂模式

```js
function createPerson(name, age, job) {
  let o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function() {
    console.log(this.name);
  };
  return o;
}
let person1 = createPerson("Nicholas", 29, "Software Engineer");
let person2 = createPerson("Greg", 27, "Doctor");
这里，函数createPerson()接收3个参数，根据这几个参数构建了一个包含
```

没有解决对象标识问题（即新创建的对象是什么类型）  
不能用 instanceof 判断对象类型

#### 构造函数模式

```js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function () {
    console.log(this.name);
  };
}
let person1 = new Person("Nicholas", 29, "Software Engineer");
let person2 = new Person("Greg", 27, "Doctor");
person1.sayName(); // Nicholas
person2.sayName(); // Greg
```

1. 构造函数与普通函数唯一的区别就是调用方式不同。
2. 构造函数的问题  
   定义的方法会重复创建，虽然是相同逻辑  
   可以把函数定义转移到构造函数外部，但全局作用域也因此被搞乱了

#### 原型模式

每个函数都会创建一个 prototype 属性，这个属性是一个对象，包含应该由特定引用类型的实例
共享的属性和方法。实际上，这个对象就是通过调用构造函数创建的对象的原型。使用原型对象的好处
是，在它上面定义的属性和方法可以被对象实例共享。原来在构造函数中直接赋给对象实例的值，可以
直接赋值给它们的原型

```js
function Person() {}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function () {
  console.log(this.name);
};
let person1 = new Person();
person1.sayName(); // "Nicholas"
let person2 = new Person();
person2.sayName(); // "Nicholas"
console.log(person1.sayName == person2.sayName); // true
```

1. 理解原型
   无论何时，只要创建一个函数，就会按照特定的规则为这个函数创建一个 prototype 属性（指向
   原型对象）。默认情况下，所有原型对象自动获得一个名为 constructor 的属性，指回与之关联的构
   造函数。对前面的例子而言，Person.prototype.constructor 指向 Person。然后，因构造函数而
   异，可能会给原型对象添加其他属性和方法。
   在自定义构造函数时，原型对象默认只会获得 constructor 属性，其他的所有方法都继承自
   Object。每次调用构造函数创建一个新实例，这个实例的内部[[Prototype]]指针就会被赋值为构
   造函数的原型对象。脚本中没有访问这个[[Prototype]]特性的标准方式，但 Firefox、Safari 和 Chrome
   会在每个对象上暴露**proto**属性，通过这个属性可以访问对象的原型。在其他实现中，这个特性
   完全被隐藏了。关键在于理解这一点：实例与构造函数原型之间有直接的联系，但实例与构造函数之
   间没有。
2. 原型层级  
	在通过对象访问属性时，会按照这个属性的名称开始搜索。搜索开始于对象实例本身。如果在这个
   实例上发现了给定的名称，则返回该名称对应的值。如果没有找到这个属性，则搜索会沿着指针进入原
   型对象，然后在原型对象上找到属性后，再返回对应的值。
3. 原型和 in 操作符
   在单独使用时，in 操作符会在可以通过对象访问指定属性时返回 true，无论该属性是在实例上还是在原型上。
   在 for-in 循环中使用 in 操作符时，可以通过对象访问且可以被枚举的属性都会返回，包括实例属性和原型属性。
4. 属性枚举顺序
   for-in 循环和 Object.keys()的枚举顺序是不确定的，取决于 JavaScript 引擎  
   Object.getOwnPropertyNames()、Object.getOwnPropertySymbols()和 Object.assign()的枚举顺序是确定性的。先以升序枚举数值键，然后以插入顺序枚举字符串和符号键。

原型动态性：  
 因为从原型上搜索值的过程是动态的，所以即使实例在修改原型之前已经存在，任何时候对原型对象所做的修改也会在实例上反映出来。

原型的问题：

1. 弱化了向构造函数传递初始化参数的能力，会导致所有实例默认都取得相同的属性值。
2. 共享特性（引用型数据，会导致修改一个实例的属性，结果全部都修改了

### 继承

继承是面向对象编程中讨论最多的话题。很多面向对象语言都支持两种继承：接口继承和实现继承。前者只继承方法签名，后者继承实际的方法。接口继承在 ECMAScript 中是不可能的，因为函数没有签名。实现继承是 ECMAScript 唯一支持的继承方式，而这主要是通过原型链实现的。

#### 原型链

ECMA-262 把原型链定义为 ECMAScript 的主要继承方式。其基本思想就是通过原型继承多个引用类型的属性和方法。

1. 默认原型  
   所有引用类型都继承自 Object
2. 原型与继承关系  
   原型与实例的关系可以通过两种方式来确定。  
	instanceof、isPrototypeof
3. 关于方法  
   子类有时候需要覆盖父类的方法，或者增加父类没有的方法。为此，这些方法必须在原型赋值之后再添加到原型上。
4. 原型链的问题
   主要问题出现在原型中包含引用值的时候。前面在谈到原型的问题时也提到过，原型中包含的引用值会在所有实例间共享，这也是为什么属性通常会在构造函数中定义而不会定义在原型上的原因。在使用原型实现继承时，原型实际上变成了另一个类型的实例。这意味着原先的实例属性摇身一变成为了原型属性。  
   第二个问题是，子类型在实例化时不能给父类型的构造函数传参。

#### 盗用构造函数

在子类构造函数中调用父类构造函数。  
因为毕竟函数就是在特定上下文中执行代码的简单对象，所以可以使用 apply()和 call()方法以新创建的对象为上下文执行构造函数。

```js
function SuperType() {
  this.colors = ["red", "blue", "green"];
}
function SubType() {
  // 继承SuperType
  SuperType.call(this);
}
let instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
let instance2 = new SubType();
console.log(instance2.colors); // "red,blue,green"
```

1. 传递参数
   相比于使用原型链，盗用构造函数的一个优点就是可以在子类构造函数中向父类构造函数传参。
2. 盗用构造函数的问题  
   盗用构造函数的主要缺点，也是使用构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此函数不能重用。此外，子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。由于存在这些问题，盗用构造函数基本上也不能单独使用。

#### 组合继承

组合继承（有时候也叫伪经典继承）综合了原型链和盗用构造函数，将两者的优点集中了起来。  
基本的思路是使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。  
这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。

```js
function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function () {
  console.log(this.name);
};
function SubType(name, age) {
  // 继承属性
  SuperType.call(this, name);
  this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function () {
  console.log(this.age);
};
let instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"
instance1.sayName(); // "Nicholas";
instance1.sayAge(); // 29
let instance2 = new SubType("Greg", 27);
console.log(instance2.colors); // "red,blue,green"
instance2.sayName(); // "Greg";
instance2.sayAge(); // 27
```

#### 原型式继承

这个 object()函数会创建一个临时构造函数，将传入的对象赋值给这个构造函数的原型，然后返回这个临时类型的一个实例。本质上，object()是对传入的对象执行了一次浅复制。

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

#### 寄生式继承

与原型式继承比较接近的一种继承方式是寄生式继承（parasitic inheritance），也是 Crockford 首倡的一种模式。  
寄生式继承背后的思路类似于寄生构造函数和工厂模式：创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象。

```js
function createAnother(original) {
  let clone = object(original); // 通过调用函数创建一个新对象
  clone.sayHi = function () {
    // 以某种方式增强这个对象
    console.log("hi");
  };
  return clone; // 返回这个对象
}
```

#### 寄生式组合继承

JavaScript 高级程序设计 P247

### 类

虽然 ECMAScript 6 类表面上看起来可以支持正式的面向对象编程，但实际上它背后使用的仍然是原型和构造函数的概念。

#### 类定义

```js
// 类声明
class Person {}
// 不会像函数声明一样提升

// 类表达式
const Animal = class {};
// 和函数表达式一样，不能再求值前引用 会等于 undefined
```

- 类可以包含构造函数方法、实例方法、获取函数、设置函数和静态类方法，但这些都不是必需的。空的类定义照样有效。
- 与函数构造函数一样，多数编程风格都建议类名的首字母要大写，以区别于通过它创建的实例。
- 类表达式的名称是可选的。在把类表达式赋值给变量后，可以通过 name 属性取得类表达式的名称字符串。但不能在类表达式作用域外部访问这个标识符。

### 类构造函数

constructor 关键字用于在类定义块内部创建类的构造函数。方法名 constructor 会告诉解释器在使用 new 操作符创建类的新实例时，应该调用这个函数。构造函数的定义不是必需的，不定义构造函数相当于将构造函数定义为空函数。

1. 实例化

```
使用new调用类的构造函数会执行如下操作。
(1) 在内存中创建一个新对象。
(2) 这个新对象内部的[[Prototype]]指针被赋值为构造函数的prototype属性。
(3) 构造函数内部的this被赋值为这个新对象（即this指向新对象）。
(4) 执行构造函数内部的代码（给新对象添加属性）。
(5) 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。
```

#### 实例、原型、类成员

类的语法可以非常方便地定义应该存在于实例上的成员、应该存在于原型上的成员，以及应该存在于类本身的成员。

1. 实例成员
   每次通过 new 调用类标识符时，都会执行类构造函数。在这个函数内部，可以为新创建的实例（this）添加“自有”属性。  
   每个实例都对应一个唯一的成员对象，这意味着所有成员都不会在原型上共享。
2. 原型方法与访问器
   为了在实例间共享方法，类定义语法把在类块中定义的方法作为原型方法。  
   类方法等同于对象属性，因此可以使用字符串、符号或计算的值作为键
3. 静态类方法
   可以在类上定义静态方法。这些方法通常用于执行不特定于实例的操作，也不要求存在类的实例。
4. 非函数原型和类成员  
   类定义并不显式支持在原型或类上添加成员数据，但在类定义外部，可以手动添加
5. 迭代器与生成器方法  
   类定义语法支持在原型和类本身上定义生成器方法

#### 继承

虽然类继承使用的是新语法，但背后依旧使用的是原型链。

1. 继承基础  
   ES6 类支持单继承。使用 extends 关键字，就可以继承任何拥有[[Construct]]和原型的对象。很大程度上，这意味着不仅可以继承一个类，也可以继承普通的构造函数
2. 构造函数、HomeObject 和 super()
   派生类的方法可以通过 super 关键字引用它们的原型。这个关键字只能在派生类中使用，而且仅限于类构造函数、实例方法和静态方法内部。在类构造函数中使用 super 可以调用父类构造函数。
3. 抽象基类
   有时候可能需要定义这样一个类，它可供其他类继承，但本身不会被实例化。虽然 ECMAScript 没有专门支持这种类的语法 ，但通过 new.target 也很容易实现。new.target 保存通过 new 关键字调用的类或函数。通过在实例化时检测 new.target 是不是抽象基类，可以阻止对抽象基类的实例化
4. 继承内置类型
   ES6 类为继承内置引用类型提供了顺畅的机制，开发者可以方便地扩展内置类型
5. 类混入

## 使用指南

## 实战经验

## 经验总结

## 信息参考
