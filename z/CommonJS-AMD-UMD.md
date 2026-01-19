---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: CommonJS-AMD-UMD
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# CommonJS-AMD-UMD

## CommonJS

CommonJS 规范概述了同步声明依赖的模块定义。这个规范主要用于在服务器端实现模块化代码组织，但也可用于定义在浏览器中使用的模块依赖。CommonJS 模块语法不能在浏览器中直接运行。

> [!NOTE] 注意
> 一般认为，Node.js的模块系统使用了 CommonJS规范，实际上并不完全正确。Node.js
使用了轻微修改版本的 CommonJS，因为 Node.js 主要在服务器环境下使用，所以不需要
考虑网络延迟问题。考虑到一致性，本节使用 Node.js 风格的模块定义语法。

### 特点

- 单例
- 缓存

CommonJS 规范概述了同步声明依赖的模块定义。  
这个规范主要用于在服务器端实现模块化代码组织，但也可用于定义在浏览器中使用的模块依赖。  
CommonJS 模块语法不能在浏览器中直接运行。

## 异步模块定义

CommonJS 以服务器端为目标环境，能够一次性把所有模块都加载到内存  
而异步模块定义（AMD，Asynchronous Module Definition）的模块定义系统则以浏览器为目标执行环境

AMD 模块实现的核心是用函数包装模块定义。

## 通用模块定义

为了统一 CommonJS 和 AMD 生态系统，通用模块定义（UMD，Universal Module Definition）规范
应运而生。  
UMD 可用于创建这两个系统都可以使用的模块代码。  
本质上，UMD 定义的模块会在启动时检测要使用哪个模块系统，然后进行适当配置，并把所有逻辑包装在一个立即调用的函数表达式（IIFE）中。
