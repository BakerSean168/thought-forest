---
tags:
  - tech/dev/frame
  - tech/framework/react
  - type/moc
description: React 框架相关入口 MOC（保留深度内容在此处）
created: 2025-12-11T10:05:00
updated: 2025-12-11T10:05:00
---

> 上级索引: [[Frame MOC]]

# React MOC

本页面为 React 生态的入口页（`type/moc`）。将深度文档、整合方案、工程化和大型示例保存在此 MOC 下的子页面或关联笔记中。

## 目标

- 汇总 React 常见模式（函数组件、Hooks）、生态库与工程化实践
- 为 SSR/Static/Native/桌面集成（Next.js/Vite/Electron/React Native）提供入口索引
- 将大型示例与架构方案保留在子页面，保持本页为导航与总览

## 快速入口

- [[React]] — 核心概念
- [[jsx]] — JSX 语法与注意事项
- [[React-Hooks]] / [[React中的钩子]] — Hooks 使用与模式
- [[Redux]] / [[Zustand]] — 状态管理库对比与实践
- [[React Router]] — 路由与导航
- [[Next.js 集成]] — SSR/SSG 相关
- [[Electron + React]] — 桌面集成

## 手工索引：React 相关文章（按子类分组，双向链接）

- 基础与语法
  - [[React]]
  - [[jsx]]

- Hooks 与模式
  - [[React-Hooks]]
  - [[React中的钩子]]

- 状态管理
  - [[Redux]]
  - [[Zustand]]
  - [[Recoil]]

- 路由与导航
  - [[React Router]]

- SSR / SSG / 服务端集成
  - [[Next.js 集成]]

- 构建与工程化
  - [[Vite / Webpack 与优化]]

- 桌面 / 原生 / 多端
  - [[Electron + React]]
  - [[React Native]]

- 编辑器 / 富文本 / 工具集成
  - [[tiptap]]
  - [[Monaco-Editor]]

- 表格 / 可视化 / 组件库
  - [[AG-Grid]]
  - [[ShadCN React MOC]]

- 项目样例与实战
  - [[krjx-olp-admin]]
  - [[krjx-olp-portal]]
  - [[todo代办任务模块的实现]]

> 说明：本页使用手工双向链接索引以便更好的浏览与双向导航。如果你希望我在被列为“深度笔记”的文件 frontmatter 中批量加入 `moc/parent: "React MOC"`，我可以先列出将被改动的文件清单供你确认。
---
tags:
  - tech/frame/react
  - type/moc
  - status/seed
description: React 相关知识索引 - 组件/状态管理/生态
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---

# ⚛️ React MOC

> React 生态相关知识的索引页面

---

## 🎯 快速导航

| 领域 | 入口 | 说明 |
|------|------|------|
| 核心 | [[React]] | React 核心概念 |
| 语法 | [[jsx]] | JSX 语法 |
| 状态管理 | [[Zustand]] | 轻量状态管理 |

---

## 📚 核心概念

- [[React]] - React 核心概念入口
- [[jsx]] - JSX 语法详解

---

## 🪝 Hooks 钩子

- [[React-Hooks]] - React Hooks 使用指南
- [[React中的钩子]] - React Hooks 面试解析

---

## 🗃️ 状态管理

- [[Zustand]] - 轻量级状态管理库

---

## 🔗 相关索引

- [[ECMAScript MOC]] - JavaScript 核心语言
- [[前端基础 MOC]] - 前端基础知识

---

## 📝 待整理

```dataview
LIST
FROM #tech/lang/react 
WHERE file.name != this.file.name
SORT file.mtime DESC
```
