---
tags: [tech/lang/typescript, type/concept, status/growing]
description: Nx
created: 2025-01-01T00:00:00
updated: 2025-12-28T17:00:35
---

> [!info] **上级索引**
> [[前端工程化 MOC]] | [[Monorepo 架构]]

---

# Nx

## 1. 什么是 Nx？

Nx 是由前 Google 工程师开发的，它借鉴了 Google 和 Facebook 内部管理海量代码的经验。它的核心目标是：**在不牺牲开发速度的情况下，帮助团队扩展（Scale）项目。**

在一个典型的 Monorepo 中，你可能会把前端（React/Vue）、后端（Node.js/NestJS）以及共享的 UI 组件库、工具函数都放在一个仓库里。Nx 的作用就是确保这些代码能和谐共处，且构建速度飞快。

## 2. Nx 的核心架构概念

理解 Nx，首先要理解它对代码组织的两个核心定义：**Apps（应用）** 和 **Libs（库）**。

### Apps vs. Libs

- **Apps (应用):** 位于 `apps/` 目录下。它们是可部署的单元（比如一个网站、一个 API 接口）。Apps 应该是“薄”的，主要负责配置和启动。
- **Libs (库):** 位于 `libs/` 目录下。这是代码逻辑真正存放的地方。
	- **Feature Libs:** 特定功能的业务逻辑。
	- **UI Libs:** 纯 UI 组件。
	- **Data-Access Libs:** 负责 API 请求和状态管理。
	- **Utility Libs:** 通用工具函数。

> **核心思想：** 鼓励你将代码尽可能拆分成小的、可复用的 Libs，然后在 Apps 中像搭积木一样引用它们。


- **Monorepo（单体仓库）**: Nx 的核心理念之一。将所有相关的项目（例如前端应用、后端服务、共享库）都放在一个代码仓库中。这样做的好处是便于代码共享、原子化提交以及统一管理依赖。
- **项目图（Project Graph）**: Nx 会分析你仓库中所有项目之间的依赖关系，并生成一个项目图。这个图是 Nx 实现许多高级功能的基础，例如，它知道如果你修改了一个库，哪些应用需要被重新测试或构建。你可以通过运行 `nx graph` 命令来可视化这个图。
- **计算缓存（Computation Caching）**: 这是 Nx 的一个“杀手级”特性。当你运行一个命令（如 `build` 或 `test`）时，Nx 会将结果缓存起来。下次你再运行同一个命令时，如果代码没有发生变化，Nx 会直接从缓存中读取结果，而不是重新执行。这大大加快了开发和 CI/CD 的速度。
- **插件（Plugins）**: Nx 通过插件来支持不同的技术栈和工具。例如，有针对 React、Angular、Node.js、Cypress 等的官方插件。这些插件提供了一系列生成器（Generators）和执行器（Executors），帮助你快速搭建和管理项目。

## 使用指南

[[Nx的教程demo尝试过程]]

下面是一些常用的 Nx 命令，可以帮助你开始：

1.  **创建新的 Nx 工作区**:

	```bash
    npx create-nx-workspace@latest my-workspace
    ```

	这个命令会引导你创建一个新的 Nx Monorepo 工作区。

2.  **生成应用或库**:
	Nx 使用生成器（generators）来自动化地创建应用和库的脚手架代码。

	```bash
    # 生成一个 React 应用
    nx g @nx/react:app my-react-app

    # 生成一个共享的 TypeScript 库
    nx g @nx/js:lib my-shared-lib
    ```

3.  **运行任务**:
	你可以使用 `nx` 命令来运行在 `project.json` 中定义的任务（Targets），例如构建、测试、启动开发服务器等。

	```bash
    # 构建名为 my-react-app 的应用
    nx build my-react-app

    # 测试名为 my-shared-lib 的库
    nx test my-shared-lib

    # 启动开发服务器
    nx serve my-react-app
    ```

4.  **查看受影响的项目**:
	当你修改了某些文件后，可以使用 `affected` 命令只对受影响的项目运行任务，这在大型仓库中尤其有用。

	```bash
    # 只构建受当前改动影响的项目
    nx affected:build

    # 只测试受当前改动影响的项目
    nx affected:test
    ```

## 配置

Nx 工作区主要由以下几个配置文件来驱动：

- **`nx.json`**: 这是整个工作区的核心配置文件。
  - `targetDefaults`: 定义了所有任务（如 `build`, `test`）的默认配置，例如默认使用哪个执行器（executor）和缓存配置。
  - `affected.defaultBase`: `affected` 命令计算变更的基准，通常是你的主分支（如 `main` 或 `master`）。
  - `plugins` / `pluginsConfig`: 配置工作区级别的插件。
- **`project.json`**: 每个项目（应用或库）目录下都有一个 `project.json` 文件。
  - `targets`: 定义了该项目可以执行的任务（例如 `build`, `serve`, `test`）。每个任务都配置了一个 `executor`，它指定了实际运行的命令或脚本。
  - `tags`: 为项目添加标签，用于项目图的可视化和模块边界的强制执行。
  - `implicitDependencies`: 显式声明一些 Nx 可能无法自动发现的依赖关系。

## 实战经验

- **合理组织代码结构**: 在 `libs` 目录下，可以按功能领域（feature-based）或按类型（type-based）来组织你的库。例如，`libs/products/ui` (按功能) vs `libs/ui/products` (按类型)。
- **使用标签（Tags）来强制模块边界**: 你可以在 `project.json` 中为项目添加标签，并配置 ESLint 规则（`nx-enforce-module-boundaries`）来限制项目之间的依赖关系。例如，你可以规定 `feature` 类型的库不能直接依赖另一个 `feature` 类型的库，而必须通过 `shared` 或 `ui` 类型的库。
- **利用 `affected` 命令提升 CI 效率**: 在你的 CI/CD 流水线中，使用 `nx affected` 命令来避免对未改动的项目进行不必要的构建和测试，可以显著缩短流水线运行时间。

### 关联文档

- [[Monorepo项目的tsconfig最佳配置实践]]
- [[nx项目中配置跨包更新（tsc）]]
- [[nx项目中在打包工具不同时的配置]]

## 参考来源

- **官方网站**: [nx.dev](https://nx.dev/) - 这是学习 Nx 最权威的地方，有详细的文档和教程。
- **官方 GitHub**: [github.com/nrwl/nx](https://github.com/nrwl/nx) - 你可以在这里找到源码、报告问题和参与社区。
- **Nx 官方博客**: [blog.nrwl.io](https://blog.nrwl.io/) - 有很多关于 Nx 最佳实践和新功能的文章。

