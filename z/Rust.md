---
tags:
  - tech/lang/rust
  - type/concept
  - status/growing
description: Rust
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[项目实践 MOC]]

---


# Rust 

## 相关工具

- [[cargo]] - Rust 包管理器和构建工具
- [[Rustup]] - Rust 工具链管理器

## 基础概念

### 简介

Rust 是一种注重性能、安全性和并发的现代系统编程语言。它在不依赖垃圾回收器的情况下实现了这些目标，使其成为其他语言难以胜任的多种应用场景的有用工具。它的语法与 C++相似，但在保持高性能的同时提供了更好的内存安全性。

## 语言基础

### 变量

- 使用 **`let`** 关键字定义 **变量**
- 所有变量默认不可变
- 使用 `mut` 关键字声明可变变量，`let mut x = 5`
- 变量默认为块作用域
- 还支持多种变量属性

### 常量

- 使用 **`const`** 关键字定义 **常量**
- 所有常量一直不允许改变，并且需要注解类型
- 常量可以在任何作用域中声明，包括全局
- 常量只能是表达式，不能是运行时计算的值，`const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;`，常量名为大写（下划线分割）、值为让人理解的计算式（60秒、60分、3小时）。

### 变量覆盖

```rust
fn main() {
    let x = 5;

    let x = x + 1;%% 5+1 %%

    {
        let x = x * 2;%% `6*2` %%
        println!("The value of x in the inner scope is: {x}");%% 12 %%
    }

    println!("The value of x is: {x}");%% 6 %%
}
```

- Rust 中变量能不断被重新定义，并覆盖作用域链前面的变量
- 等离开作用域后，重新使用前面的变量。
- 并且定义变量时还能够读取前一个变量（底层实现是先获取值，再定义变量吗？）  
- mut 是修改变量的值（不能修改类型），变量覆盖 是 重定义变量
// 总是最上方的定义起作用，把新的定义压入栈中，离开作用域时抛出

### 类型

| **类别**    | **类型**                                | **描述**                         | **示例**                                                          |
| --------- | ------------------------------------- | ------------------------------ | --------------------------------------------------------------- |
| **标量类型**  | `i8`/`i16`/`i32`/`i64`/`i128`/`isize` | 有符号整数（位数对应存储大小，`isize` 指针大小相关） | `let x: i32 = 42;`                                              |
|           | `u8`/`u16`/`u32`/`u64`/`u128`/`usize` | 无符号整数                          | `let y: u8 = 255;`                                              |
|           | `f32`/`f64`                           | 浮点数（单精度/双精度）                   | `let z: f64 = 3.14;`                                            |
|           | `bool`                                | 布尔值（`true`/`false`）            | `let flag: bool = true;`                                        |
|           | `char`                                | Unicode 字符（4字节）                | `let c: char = '🦀';`                                           |
| **复合类型**  | `tuple`                               | 固定长度、异构集合                      | `let t: (i32, f64) = (5, 3.0);`                                 |
|           | `array`                               | 固定长度、同构集合（栈分配）                 | `let arr: [i32; 3] = [1, 2, 3];`                                |
| **字符串**   | `&str`                                | 字符串切片（不可变引用）                   | `let s: &str = "hello";`                                        |
|           | `String`                              | 动态字符串（堆分配）                     | `let s = String::from("hi");`                                   |
| **指针类型**  | `&T`                                  | 不可变引用                          | `let ref_x: &i32 = &x;`                                         |
|           | `&mut T`                              | 可变引用                           | `let ref_mut: &mut i32 = &mut x;`                               |
|           | `Box<T>`                              | 堆分配指针                          | `let boxed = Box::new(5);`                                      |
| **特殊类型**  | `()`                                  | 单元类型（类似空值）                     | `let unit: () = ();`                                            |
|           | `!`                                   | Never类型（表示永不返回）                | `fn panic() -> ! { panic!() }`                                  |
| **泛型容器**  | `Vec<T>`                              | 动态数组（堆分配）                      | `let vec: Vec<i32> = vec![1, 2];`                               |
|           | `HashMap<K, V>`                       | 哈希映射                           | `use std::collections::HashMap;`<br>`let map = HashMap::new();` |
| **自定义类型** | `struct`                              | 结构体                            | `struct Point { x: i32, y: i32 }`                               |
|           | `enum`                                | 枚举（可带数据）                       | `enum Message { Quit, Write(String) }`                          |

---

1. **数字字面量增强**：

```rust
let decimal = 98_222;// 十进制（可加_分隔）
let hex = 0xff;// 十六进制
let byte = b'A';// u8字节字面量
```

2. **类型推断**：
Rust 支持类型推断，多数场景可省略显式类型声明：

```rust
let x = 5;// 自动推断为 i32
let y = 3.0;// 自动推断为 f64
```

3. **类型转换**：
- 必须显式使用 `as` 转换：

```rust
let x: i32 = 42;
let y: u8 = x as u8;
```

### 函数

```rust
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

- 使用 **`fn`** 关键字声明 **函数**
- Rust 代码使用蛇形命名法（ _snake case_）作为函数和变量名称的常规风格，其中所有字母都是小写，下划线分隔单词。

### 参数

```rust
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

- 函数签名中一定要给变量类型注解
- 使用 `{}` 来包裹变量

### 语句和表达式

```rust
fn main() { let x = (let y = 6); }

// 报错，因为 let y = 6 是语句，没有返回值，x 没有可绑定的东西 %%
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found `let` statement
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^
  |
  = note: only supported directly in conditions of `if` and `while` expressions

warning: unnecessary parentheses around assigned value
 --> src/main.rs:2:13
  |
2 |     let x = (let y = 6);
  |             ^         ^
  |
  = note: `#[warn(unused_parens)]` on by default
help: remove these parentheses
  |
2 -     let x = (let y = 6);
2 +     let x = let y = 6;
  |

warning: `functions` (bin "functions") generated 1 warning
error: could not compile `functions` (bin "functions") due to 1 previous error; 1 warning emitted
```

- 语句是执行某些操作的指令，并且不返回值。
- 表达式求值得到一个结果值。
- `;` 是区分语句和表达式的关键之一

### 函数的返回值

```rust
// 第一种
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1
}

//第二种
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

第一种能正确运行，但第二种不能：  
`x + 1;` 后面的 `;` 表示这是一个语句，就没有返回值了

### 注释

- `//`
- 多行的话，每行都要添加

### `if`

- 可以赋值，`let number = if condition { 5 } else { 6 };`，此时 返回的类型应保持相同
- 明确使用 boolean，不会像 js 一样转换

### `loop`

```rust
fn main() {
    loop {
        println!("again!");
    }
}

// 使用 breake 返回
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```
- 无限重复
- 标签循环、嵌套循环

### `while`

- 条件循环
### `for`

- 遍历循环

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}

fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}
```

## 所有权（Ownership）

所有权是 Rust 最独特的特性，对整个语言有着深远的影响。它使 Rust 能够在不需要垃圾收集器的情况下提供内存安全保证，因此理解所有权的工作原理非常重要。在本章中，我们将讨论所有权以及几个相关的特性：借用、切片以及 Rust 如何在内存中布局数据。

### 栈 和 堆

**栈的描述**：
栈按接收值的顺序存储值，并按相反的顺序移除值。这被称为后进先出。想象一下堆叠的盘子：当你添加更多盘子时，你会把它们放在最上面，当你需要盘子时，你会从最上面拿一个。从中间或底部添加或移除盘子效果会差很多！向栈中添加数据称为推入栈，移除数据称为弹出栈。栈中存储的所有数据都必须具有已知的固定大小。

**堆的描述**：
堆的内存组织较为松散：当你把数据放在堆上时，你会请求一定量的空间。内存分配器在堆中找到一个足够大的空闲位置，将其标记为已使用，并返回一个指针，该指针是那个位置的地址。这个过程称为在堆上分配内存，有时也简称为分配（将值压入栈中不被视为分配）。由于堆指针的大小是已知的、固定的，你可以将指针存储在栈上，但当你需要实际的数据时，你必须跟随指针。想象一下你在餐厅就座。当你进入时，你会报上你的人数，服务员会找到一个能容纳所有人的空桌并带你们过去。如果你的人来晚了，他们可以询问你们在哪里就座，从而找到你们。

- 栈中存储的所有数据都必须具有已知的固定大小；堆的内存组织较为松散，可以任意大小。
- 存储和读取速度都是栈中更快。
- 所有权的主要目的是管理堆内存数据。

### 所有权规则

- 每个值在 Rust 中都有一个所有者。
- 任何时候只能有一个所有者。
- 当所有者超出作用域时，该值将被丢弃。
- 移动而非浅拷贝

### 变量作用域

```rust
    {                      // s is not valid here, since it's not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid

```

- 当 `s` 进入作用域时，它是有效的。
- 它保持有效直到离开作用域。

### 函数与所有权与所有权转移

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope
	println!("{some_string}");
    takes_ownership(s);             // s's value moves into the function...
    // 会报错，因为 所有权会在调用函数时，转移给函数，然后被释放
    println!("{some_string}");   //  ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // Because i32 implements the Copy trait,
                                    // x does NOT move into the function,
                                    // so it's okay to use x afterward.

} // Here, x goes out of scope, then s. However, because s's value was moved,
  // nothing special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{some_string}");
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{some_integer}");
} // Here, some_integer goes out of scope. Nothing special happens.
```

### 引用和借用

上面的 函数与所有权与所有权转移，无疑有点奇怪，s 传进去后就无了；其实有方法能避免这个问题，那就是 `&` 引用
创建引用的行为为借用。  

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{s1}' is {len}.");
}
// s 会引用 s1
fn calculate_length(s: &String) -> usize {
    s.len()
}
```

#### 可变引用

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}

// 创建两个对 `s` 的可变引用的代码,会报错
let mut s = String::from("hello");

let r1 = &mut s; ！！！
let r2 = &mut s; ！！！

println!("{r1}, {r2}");// error

```

- 不能在同一个作用域同时创建两个对 `s` 的可变引用的代码,会报错（为了避免数据竞争，rust直接不允许这个行为）

#### 引用规则

- 在任何时候，你只能有一个可变引用，或者有任意数量的不可变引用。
- 引用必须始终有效。

### 切片

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}

```

一个类似 js 中的 slice 的自带函数，应该内部实现是 通过 可变引用 传入，再返回修改后的变量

```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!
    println!("the first word is: {word}");
    // clear 和 println 会同时产生两个引用，导致报错
}
```

## 结构体

### 定义

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

// 生成不可变
fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };
}

// 生成可变
fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

- 结构体中的属性一般是不使用 引用的，希望每个该结构体的实例都拥有其所有数据，并且这些数据在结构体有效期间始终有效。
- 结构体中可以使用引用，但需要声明周期说明符
#### 个人理解

- 结构体类似 js 中的对象、类型
### 结构体的应用和 `println` 、`debug`

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {rect1:?}");
}

#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
```

## 枚举

我们直接将数据附加到枚举的每个变体上，因此不需要额外的结构体。在这里，它也更容易看出枚举如何工作的另一个细节：我们定义的每个枚举变体的名称也成为一个构造枚举实例的函数。也就是说， `IpAddr::V4()` 是一个函数调用，它接受一个 `String` 参数并返回一个 `IpAddr` 类型的实例。我们自动得到这个构造函数，这是定义枚举的结果。

```rust
// 1
enum IpAddrKind {
	V4,
	V6,
}

struct IpAddr {
	kind: IpAddrKind,
	address: String,
}

let home = IpAddr {
	kind: IpAddrKind::V4,
	address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
	kind: IpAddrKind::V6,
	address: String::from("::1"),
};

// 2
enum IpAddr {
	V4(String),
	V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));

```


### `match`

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}

```

## Packages、Crates and Modules

- **Packages**: A Cargo feature that lets you build, test, and share crates  
    包：Cargo 的一个功能，允许您构建、测试和共享 Crate
- **Crates**: A tree of modules that produces a library or executable  
    Crate：一个模块树，生成库或可执行文件
- **Modules and use**: Let you control the organization, scope, and privacy of paths  
    模块和 use：让您控制路径的组织结构、作用域和隐私
- **Paths**: A way of naming an item, such as a struct, function, or module  
    路径：一种命名项目的方式，例如结构体、函数或模块
### 相关工具

- 语言版本管理工具[[Rustup]]
- 包管理器[[cargo]]

## 使用指南

### 安装配置

[Install Rust - Rust Programming Language](https://rust-lang.org/tools/install/)

### 编辑器

- VSCode
- RustRover
- Sublime Text
- Vim

### Rust REPL (Rust Playground)

一个交互式外壳，你可以在其中实时编写和测试 Rust 代码片段。与在 Rust 中通常运行程序时需要手动编译然后运行程序不同，REPL 会自动求值你的输入，执行后立即返回结果。这在尝试 Rust 代码、学习语言和调试时很有帮助。REPL 不是直接构建在 Rust 中的，但可以通过第三方工具如 `evcxr_repl` 获得。

[Rust Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2024)

## 实战经验

## 经验总结

## 信息参考
