---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript逻辑语句与条件控制
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]]

---


# ECMAScript 逻辑语句

## 基础概念

ECMAScript（JavaScript 的标准）中的逻辑语句用于控制程序的执行流程，主要包括条件语句、循环语句和跳转语句。这些语句允许代码根据条件执行不同分支、重复执行代码块，或在特定情况下中断/继续执行。

### 条件语句

- **if-else**：根据条件执行代码块，支持嵌套和 else if。
- **switch**：多分支选择，适用于固定值比较。

### 循环语句

- **for**：计数循环，适用于已知循环次数。
- **while**：条件循环，先判断条件再执行。
- **do-while**：条件循环，先执行再判断，至少执行一次。
- **for-in**：遍历对象属性（ES3）。
- **for-of**：遍历可迭代对象（如数组、字符串，ES6）。

### 跳转语句

- **break**：退出当前循环或 switch。
- **continue**：跳过当前循环迭代，继续下一次。
- **return**：退出函数并返回值（函数内使用）。

## 使用指南

### 条件语句使用

```javascript
// if-else 示例
let age = 18;
if (age >= 18) {
  console.log("成年");
} else {
  console.log("未成年");
}

// switch 示例
let day = 1;
switch (day) {
  case 1:
    console.log("星期一");
    break;
  default:
    console.log("其他");
}
```

### 循环语句使用

```javascript
// for 循环
for (let i = 0; i < 5; i++) {
  console.log(i); // 0,1,2,3,4
}

// while 循环
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// for-of 循环（ES6）
let arr = [1, 2, 3];
for (let item of arr) {
  console.log(item); // 1,2,3
}
```

### 跳转语句使用

```javascript
// break 和 continue
for (let i = 0; i < 10; i++) {
  if (i === 5) break; // 退出循环
  if (i % 2 === 0) continue; // 跳过偶数
  console.log(i); // 1,3
}

// return（函数内）
function test() {
  return "结束";
}
```

## 实战经验

- **条件语句**：优先使用 `===` 避免类型转换错误。switch 适合枚举值，但记得加 break 防止穿透。
- **循环语句**：for-of 比 for-in 更安全（避免原型链污染）。避免在循环中修改数组长度（可能导致无限循环）。
- **跳转语句**：break 用于退出，continue 用于跳过。return 只在函数中使用。
- **性能考虑**：for 循环最快，while 适用于不确定次数。避免在循环体内创建函数（闭包开销）。
- **常见陷阱**：忘记 break 在 switch 中；while 条件永真导致死循环；for-in 遍历数组时索引为字符串。

## 经验总结

- **最佳实践**：使用 let/const 声明循环变量（ES6）。优先选择语义清晰的语句（如 for-of 遍历数组）。
- **避免错误**：始终检查条件边界；使用严格比较；嵌套循环时注意变量作用域。
- **现代化**：ES6+ 推荐 for-of 和箭头函数结合使用。避免 var（作用域问题）。
- **调试技巧**：使用 console.log 检查循环变量；注意异步代码中的循环（可能需要 Promise 或 async/await）。

## 信息参考

- [MDN 条件语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/if...else)
- [MDN 循环语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for)
- [MDN 跳转语句](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/break)
- [阮一峰 ES6 入门 - 循环](https://es6.ruanyifeng.com/#docs/iterator)

