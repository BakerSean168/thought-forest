---
tags:
  - tech/dev/javascript
  - type/concept
  - status/evergreen
description: JavaScript 语言基础与进阶
created: 2025-09-29T18:17:52
updated: 2025-12-07T17:00:00
---

# JavaScript

**上级索引**：[[ECMAScript MOC]]

知识来源：  
[阮一峰 JavaScript 教程](https://www.bookstack.cn/read/javascript-tutorial/README.md)  
《JavaScript 高级程序设计（第 4 版）》  
https://javascript.info/

## 基础概念

### JavaScript 是什么

起初用于在前端进行数据验证的脚本语言，减少与服务器的通信次数。

是 [[z/ECMAScript | ECMAScript]] 的实现

[[JavaScript-in-Browser |浏览器下的JavaScript]] （ECMAScript+浏览器相关封装工具）：

- 核心（ECMAScript）
- 文档对象模型（DOM）  
   提供 HTML、XML 的 API  
   操控表示文档的树
- 浏览器对象模型（BOM）  
   提供 浏览器对象 的 API

[[JavaScript-in-Nodejs | Node.js下的JavaScript]] （ECMAScript+相关工具）：

- **ECMAScript**：同样是核心语言规范，Node.js 基于 V8 引擎实现相同的 ECMAScript 标准。
- **DOM**：不存在，因为 Node.js 是服务器端运行时，没有浏览器文档树。
- **BOM**：不存在，Node.js 没有浏览器窗口等概念。
- **替代部分**：Node.js 提供了自己的 API 和模块系统，包括：
	- 文件系统（fs 模块）
	- 网络（http/https 模块）
	- 操作系统（os 模块）
	- 进程管理（process 对象）
	- 事件循环（基于 libuv）

### [[JS中的事件循环 | 事件循环（浏览器 and Nodejs对比）]]



### ES6 有哪些新特性

1. let、const
2. 箭头函数
3. 模板字符串
4. 解构赋值
5. 默认参数
6. 扩展运算符
7. 类与模块
8. Promise
9. Symbol 和迭代器
10. 新的数据结构：Map、Set
11. 对象属性简写，属性方法简写

### ajax-axios-fetch

#### Ajax

Asynchronous JavaScript and XML，异步的 JS 和 XML  
AJAX 是一种基于 XMLHttpRequest（XHR）对象的异步通信技术，允许浏览器与服务器交换数据并局部更新页面，而无需刷新整个页面。其核心原理是通过 JavaScript 发起 HTTP 请求，处理响应数据后，使用 DOM 操作动态更新网页内容。

1. 创建 XMLHttpRequest 对象
2. 配置请求
3. 设置回调函数
4. 发送请求
5. 处理响应数据

#### Axios

Axios 是一个基于 Promise 的 HTTP 客户端库，封装了 XHR 对象，支持浏览器和 Node.js 环境。它简化了请求配置，并提供了拦截器、自动 JSON 转换、取消请求等高级功能

1. 发送请求
2. 响应数据
3. 请求和响应拦截器
4. 错误处理

#### Fetch

现代的网络请求 API，用于在浏览器中发起 HTTP 请求。它是 XMLHttpRequest 的升级版，提供了更简洁和强大的接口，并且原生支持 HTTP/2 的一些特性

优点：

- 原生支持 Promise
- 提供了请求和响应对象（Request、Response）
- 支持 HTTP/2 流式处理  
   通过 Response.body 获取到原始的响应流

不足：

- 不会自动携带 cookie
- 无法原生取消请求
- 无法原生监控请求进度

### 深拷贝和浅拷贝

浅拷贝：  
只复制对象的第一层属性，如果属性是 引用类型（如对象、数组），则拷贝的是 引用地址，而不是实际的值。

- Object.assign()
- 展开运算符...
- Array.prototype.slice()

深拷贝：  
递归复制对象的所有层级，包括嵌套的引用类型，生成一个 完全独立的新对象。

- JSON.parse(JSON.stringify(obj))
- 递归
- structuredClone()

## OTHER

### for in 和 for of

| **对比维度**         | **`for...in`**                                         | **`for...of`**                                                |
| -------------------- | ------------------------------------------------------ | ------------------------------------------------------------- |
| **遍历对象类型**     | 普通对象、数组等可枚举属性（包括原型链属性）           | 可迭代对象（数组、字符串、Map、Set、TypedArray、NodeList 等） |
| **遍历内容**         | 对象的键名（Key）或数组的索引（字符串形式）            | 可迭代对象的值（Value）                                       |
| **适用数据结构**     | 对象、数组（不推荐用于数组遍历）                       | 数组、字符串、Map、Set 等支持迭代器的数据结构                 |
| **原型链属性处理**   | 会遍历原型链上的可枚举属性，需用 `hasOwnProperty` 过滤 | 仅遍历对象自身的可迭代值，不涉及原型链                        |
| **循环控制语句支持** | 支持 `break`、`continue`                               | 支持 `break`、`continue`                                      |
| **返回值类型**       | 数组索引为字符串（如 `"0"`）                           | 数组元素为原始类型或对象（如 `1`、`{name: 'John'}`）          |
| **顺序保证**         | 不保证顺序（对象属性无序）                             | 按可迭代对象内部顺序遍历（如数组按索引顺序）                  |
| **自定义迭代能力**   | 无                                                     | 可通过实现 `Symbol.iterator` 方法使普通对象可迭代             |

### forEach() 和 map()

forEach()和 map()方法通常用于遍历 Array 元素

forEach()方法：  
返回 undefined  
不能与其他方法链接

map()  
返回一个包含已转换元素的新数组  
可以链接  
性能更好
