---
tags:
  - tech/lang/nodejs
  - type/concept
  - status/growing
description: JavaScript-in-Nodejs
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Node.js MOC]] | [[后端框架 MOC]]

---


# JavaScript-in-Nodejs 

## 基础概念

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，主要用于构建高性能、可扩展的服务器端应用。其核心优势在于事件驱动、非阻塞 I/O 模型，适合处理高并发和 I/O 密集型任务。 
### 特性

- 高性能  
- 非阻塞I/O  
- 单进程异步  
- 事件驱动  
- 轻量级与高扩展性  
  跨平台、生态大  
### 全局对象

在任何地方都能直接访问的对象。这些对象提供了一些基本的功能和常用的工具函数.  

#### global

Node.js 的全局命名空间对象，类似浏览器中的 window 对象。  

#### process

全局对象，提供了有关当前 Node.js 进程的信息和控制，让我们可以与操作系统进行交互。  

常用属性  
- process.argv
- process.env
- process.exit()
- process.cwd()
- process.memoryUsage()
- process.uptime()
- process.nextTick(callback)

#### __dirname and __filename

__dirname:  
	当前脚本文件所在目录的绝对路径

__filename:  
当前脚本的绝对路径

#### console

用于打印标准输出和错误输出信息  

#### module exports

系统模块相关，用于导出和导入模块

#### 定时器函数

setTimeout(), clearTimeout(), setInterval(), clearInterval()  

#### Buffer

Buffer 对象是 Node.js 中的一个全局类，用来在处理二进制数据时，提供对内存缓冲区的操作能力。  
作用：  
- 处理二进制数据
- 与流结合  
	文件处理、网络传输  
- 转换编码格式  

### 包管理器

#### npm

Node Package Manager

用于安装、共享、管理和控制 JavaScript 包及其依赖版本  
- 初始化项目
- 安装依赖  
- 保存依赖
- 更新依赖
- 删除依赖
- 全局安装和局部安装
- npm scripts
- 版本范围控制
- npx
- 私有 npm 仓库
- 依赖安全性检查
- 锁文件

### 特殊文件

package.json  
- 描述项目信息
- 定义项目依赖
- 管理脚本命令
- 项目其他配置信息

### Promise 的特点

- 状态不可逆性
  三种明确状态-Promise 的状态只能是以下三者之一：  
	Pending（进行中）：初始状态，尚未完成或拒绝。  
	Fulfilled（已兑现）：异步操作成功完成，通过 resolve() 触发。  
	Rejected（已拒绝）：异步操作失败，通过 reject() 或抛出异常触发。 状态一旦从 Pending 变为 Fulfilled 或 Rejected，便不可逆且不可修改。  
  状态由内部结果决定  
	Promise 的状态仅由异步操作的结果决定，外部无法直接干预，保证了操作的原子性   
- 链式调用  
- 无法取消  
- 微任务队列  
  执行时机早于宏任务  
- 静态方法支持复杂场景  
  Promise.all()：并行执行多个 Promise，全部成功时返回结果数组，任一失败则整体失败。  
  Promise.race()：返回首个完成的 Promise 结果。  
  Promise.resolve()/reject()：快速创建已确定状态的 Promise  

### 同步和异步

同步：  
顺序执行  

异步：
不会阻塞后续代码的执行。异步操作会在完成时通过回调函数、Promise 以及 async/await 等机制通知相关的代码去处理结果。  

### 事件循环

node.js 处理异步操作的基础  

| 阶段名称            | 执行任务                                                                    | 示例 API/场景                     |
| ------------------- | --------------------------------------------------------------------------- | --------------------------------- |
| **Timers**          | 执行 `setTimeout` 和 `setInterval` 的回调。                                 | `setTimeout(fn, 0)`               |
| **Pending I/O**     | 处理系统操作（如 TCP 错误、文件读写）的完成回调。                           | `fs.readFile` 的完成回调          |
| **Idle/Prepare**    | Node.js 内部使用（轮询准备阶段，开发者通常无需关注）。                      | -                                 |
| **Poll**            | 1. 检索新的 I/O 事件并执行回调。<br>2. 计算阻塞时间（等待下一个事件循环）。 | `net.Server` 的连接事件           |
| **Check**           | 执行 `setImmediate` 的回调（在 Poll 阶段完成后立即执行）。                  | `setImmediate(fn)`                |
| **Close Callbacks** | 执行关闭事件的回调（如 `socket.on('close', ...)`）。                        | `socket.destroy()` 触发的关闭回调 |

依赖一个名为 Libuv 的库（事件驱动的跨平台非阻塞 I/O 库。  

### require 和 import

引入模块的两种不同方式  
require  
- 基于 CommonJS  
- 同步加载模块并立即执行  
- 原生支持  

import  
- 基于 ES6  
- 异步加载  
- 需要 babel 等编译器支持  

最近的 Node.js 版本中（14.0.0及以上），原生支持 ESM

### node.js 模块的加载机制

主要依赖 CommonJS 规范。实现了模块的导入和导出，让开发者可以将代码分成独立的文件和模块进行组织、复用。
加载过程包括路径解析、文件类型识别、编译和缓存等步骤。  

### 回调、Promise、async/await

处理异步操作的三种不同方式

| 特性         | 回调（Callback）                                  | Promise                                     | async/await                                                   |
| ------------ | ------------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------- |
| **语法结构** | 嵌套回调（易产生“回调地狱”）                      | 链式调用（`.then()`、`.catch()`）           | 同步代码风格（`async`、`await` 关键字）                       |
| **错误处理** | 手动传递错误（如 `if (err) {}`）                  | 通过 `.catch()` 统一捕获                    | 通过 `try/catch` 捕获                                         |
| **可读性**   | 嵌套层级深时难以维护                              | 链式结构更清晰                              | 代码结构最接近同步逻辑，易读性强                              |
| **链式调用** | 需手动嵌套回调                                    | 支持链式调用（可返回新 Promise）            | 隐式链式调用（通过顺序执行 `await`）                          |
| **兼容性**   | 所有 JavaScript 环境                              | ES6+（需 Polyfill 兼容旧浏览器）            | ES2017+（需 Babel 转译）                                      |
| **适用场景** | 简单异步操作、传统 Node.js 库（如 `fs.readFile`） | 复杂异步流程（如多级依赖请求）              | 需要同步代码风格的异步操作（如逻辑复杂的异步控制）            |
| **错误冒泡** | 需逐层传递错误                                    | 支持冒泡到最近的 `.catch()`                 | 支持冒泡到外层 `try/catch`                                    |
| **返回值**   | 无（通过回调函数返回结果）                        | 返回 Promise 对象                           | 返回 Promise 对象（`async` 函数）                             |
| **并行处理** | 需手动实现（如计数器或第三方库）                  | 通过 `Promise.all()`、`Promise.race()` 处理 | 结合 `Promise.all()` 使用（如 `await Promise.all([p1, p2])`） |
| **调试体验** | 堆栈信息不连贯                                    | 堆栈信息较清晰                              | 堆栈信息清晰（模拟同步调试）                                  |

回调函数就是用参数传递给另一个函数的函数  

## JSON 数据

- JSON.prase  
	JSON 数据转为 JS 对象  
- JSON.stringify  
	JS 对象转为 JSON 数据  
- 通常异步读取
- 验证 JSON 数据  
	ajv库

## 如何在 Node.js 中高效处理日志，避免影响性能  

- 异步记录  
- 日志级别控制  
- 日志轮转  
	限制单个日志文件大小  
- 批量写入  
- 缓冲机制

## 模块

### crypto

- crypto 是 Node.js 的核心模块，无需额外安装  
- 它提供了加密功能，包括对 OpenSSL 的哈希、HMAC、加密、解密、签名和验证功能的一整套封装。  
## 使用指南

## 实战经验

[[Nodejs的包管理器]]

## 信息参考

