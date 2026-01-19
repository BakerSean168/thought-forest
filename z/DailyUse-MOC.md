---
tags:
  - tech/dev/project/dailyuse
  - type/moc
  - status/growing
description: DailyUse 桌面应用项目开发文档索引，主要记录一些收获知识点或者一些通用的架构思考等，具体细节应该记录在项目文档中。
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[项目实践 MOC]] | [[000_Home]]

---

# 🖥️ DailyUse 项目 MOC

> DailyUse 是一个基于 Electron + Vue3 + Express 的个人效率管理桌面应用，采用 Monorepo 架构和 DDD 设计模式。

---

## 🎯 项目概览

- [[z/DailyUse]] - 项目总览与开发计划
- [[DailyUse 开发日记]] - 开发日志记录

---

## 📦 功能模块

### 核心业务模块

| 模块 | 说明 | 文档 |
|------|------|------|
| Account | 账号认证与用户管理 | [[Account 模块]] |
| Goal | 目标与 OKR 管理 | [[Goal 模块]] |
| Task | 任务管理 | [[Task 模块]] |
| Schedule | 日程安排 | [[Schedule 模块]] |
| Reminder | 定时提醒 | [[Reminder 模块]] |
| Notification | 通知中心 | [[Notification 模块]] |

### 辅助功能模块

- [[Authentication 模块]] - 身份验证模块
- [[Editor 模块]] - 编辑器模块
- [[Repository 模块]] - 数据仓储模块
- [[Knowledge-Doc模块]] - 知识文档模块
- [[dashboard模块]] - 仪表盘模块
- [[AI模块]] - AI 功能集成

---

## 🔧 技术实现

### 模块开发经验

- [[goal模块中数据初始化与持久化与同步问题]] - Goal 模块数据同步
- [[todo代办任务模块的实现]] - 待办任务实现
- [[弹窗通知模块]] - 通知弹窗实现
- [[定时提醒功能实现]] - 定时提醒服务
- [[定时提醒服务的前端数据收集、处理]] - 提醒数据处理

### 功能实现

- [[DailyUse 的logout功能实现]] - 登出功能
- [[DailyUse 使用的事件方法总结]] - 事件系统
- [[账号信息记录功能实现]] - 账号信息记录
- [[优雅的弹窗（ConfirmDialog）实现]] - 弹窗组件

### Electron 开发

- [[Electron-Vue3 下的弹窗提醒服务实现]] - 弹窗提醒
- [[Electron-Vue3 下的多账号时进程通信携带标识符的方式]] - 多账号通信
- [[Electron-Vue3实现双窗口]] - 双窗口实现
- [[Electron-Vue3项目数据的本地存储]] - 本地存储
- [[electron-vue3项目的本地数据缓存策略]] - 缓存策略
- [[electron-vue3中使用better-sqlite3遇到的问题]] - SQLite 问题
- [[Electron项目的数据存储]] - 数据存储方案
- [[Electron项目问题]] - 常见问题汇总

### 后端开发

- [[express项目结构]] - Express 项目结构
- [[express实现登录]] - 登录实现
- [[express路由导致的认证问题]] - 路由认证问题

---

## 🏗️ 架构与工程化

### Monorepo 配置

- [[Nx配置完整指南]] - Nx 完整配置
- [[nx项目中配置跨包更新（tsc）]] - 跨包更新
- [[nx项目中在打包工具不同时的配置]] - 打包工具配置
- [[monorepo架构中开发时直接链接原始代码]] - 源码链接

### 工程化实践

- [[项目工程化实践（Vite）]] - Vite 工程化
- [[Vitest-in-dailyuse]] - Vitest 测试配置
- [[TDD_Workflow]] - 测试驱动开发
- [[配置「eslint-Prettier」]] - 代码规范配置
- [[Husky-lint-staged]] - Git 提交检查

### 设计模式

- [[DDD 架构]] - DDD 领域驱动设计
- [[Monorepo 架构]] - Monorepo 单仓多包
- [[事件驱动]] - 事件驱动架构
- [[事件总线下多模块业务原子性问题]] - 事件总线问题

---

## 🐛 问题排查

- [[Dailyuse中遇到的问题总结]] - 问题汇总
- [[时间队列模块报错]] - 时间队列错误
- [[ipc事件处理器注册不成功]] - IPC 注册问题
- [[vue全局对话框没反应问题]] - 对话框问题
- [[无法找到-rollup-rollup-win32-x64-msvc]] - Rollup 依赖问题

---

## 🌐 Web 端与部署

- [[web端实现计划]] - Web 端规划
- [[web首屏加载速度优化]] - 首屏优化
- [[虚拟机上部署postgresql]] - PostgreSQL 部署

---

## Api 端与部署

- [[DailyUse的api端部署]]

---

## 🔗 相关 MOC

- [[项目实践 MOC]] - 项目实践经验
- [[前端工程化 MOC]] - 前端工程化
- [[Electron]] - Electron 框架
- [[Vue3 MOC]] - Vue3 框架
- [[Node.js MOC]] - Node.js 后端
