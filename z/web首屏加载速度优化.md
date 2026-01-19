---
tags:
  - tech/dev/frontend
  - type/concept
  - status/seed
description: web首屏加载速度优化
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---


# web首屏加载速度优化 

## 问题详情

我的 dailyuse 首屏加载速度很慢：
进入登录页加载了近 20s。
![[Pasted image 20251113140310.png]]
## 解决方案

### 1. 路由懒加载

没有生效的原因：
- 在 [main.ts](vscode-file://vscode-app/d:/usr/VSCode-win32-x64-1.105.1/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 里我们同步导入了 `./shared/initialization/AppInitializationManager`，这个管理器在顶层立即 `import` 了 `modules/goal`, `modules/task`, `modules/reminder` 等子模块的初始化方法（参见 [AppInitializationManager.ts](vscode-file://vscode-app/d:/usr/VSCode-win32-x64-1.105.1/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 的 [registerGoalInitializationTasks()](vscode-file://vscode-app/d:/usr/VSCode-win32-x64-1.105.1/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 等调用）。
- 对于 Vite 而言，只要存在这样的静态依赖，相关代码就会被打进首屏 chunk，即便路由组件本身是通过 `() => import('...')` 的形式懒加载的。懒加载只作用于组件本身，不会阻止这些同步初始化代码被提前打包和执行。
- 因此，即使停留在 `/auth` 登录页，初始化管理器已经把 goal / task 等模块的 store、service、composable 全部拉进来了，自然会看到大量模块代码在首批资源里。


1. 先总结一下你做了那些修改，将首屏加载优化到了 8329kb
2. 继续分析一下，为什么有的加载的是 chunk，有的是单独的，比如 goal、index等；首先这代表在首屏加载前就已经 还是加载了 业务代码，需要移除，其次，这是代表这部分代码没有被 chunk 吗，这是不是会让 请求次数增加，也会导致加载变慢

![[Pasted image 20251113200254.png]]

### 2. 国际化配置按需加载

多余 common 配置，直接加载，其余业务 打包为 zh-CN-index.js chunk，减少请求次数（6个)和首页加载大小(60KB)
## 实战经验
## 经验总结
## 信息参考
