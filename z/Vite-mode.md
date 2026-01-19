---
tags: [status/growing, type/concept, tech/dev]
description: 本文简单讲讲 Vite 的 Mode 模式。
tool: vite
created: 2025-12-30T16:43:11
updated: 2025-12-30T16:50:47
---

# Vite-mode 

在 vite 中的字段为 mode

在现代前端开发中，Vite 的 **Mode（模式）** 是一个至关重要的概念。它不仅是一个简单的字符串标签，更是一套成熟的环境差异化管理方案。

---

## 1. 为什么会出现 Mode？（解决什么痛点）

在 Mode 概念成熟之前，开发者通常依赖 `process.env.NODE_ENV` 来区分开发和生产环境。但随着项目复杂化，这种方式暴露了几个缺陷：

- **二元对立的局限性**：只有 "development" 和 "production" 远远不够。现实中我们有：本地开发、内网测试（Staging）、灰度发布（Preview）、正式生产等多个阶段。
- **配置硬编码**：如果没有 Mode 系统，开发者可能需要在代码里写大量的 `if/else` 来切换 API 地址，这极易导致发布时漏改配置。
- **构建优化需求不同**：测试环境可能需要开启 SourceMap 方便排查问题，而生产环境则需要彻底压缩代码并移除调试日志。

Vite 引入 Mode 机制，就是为了**将“环境标识”与“构建配置/环境变量”解耦**，让开发者可以自由定义任何环境。

---

## 2. Vite Mode 的核心工作机制

Mode 的本质是一个**指令开关**。当你运行 Vite 命令时，可以显式指定它：

- `vite --mode staging`
- 在 Nx 中，则是通过 `project.json` 的 `configurations` 传递该参数。

### 环境文件加载逻辑

一旦确定了 Mode（例如 `staging`），Vite 会启动它的“自动装载机”：

1. **加载基础变量**：先读 `.env`。
2. **加载模式变量**：读取 `.env.staging`，其中的变量会覆盖 `.env` 中的同名变量。
3. **本地覆盖（非必选）**：如果存在 `.env.staging.local`，它会拥有最高优先级（通常用于存放个人私密 Token，且不提交 Git）。

---

## 3. Mode 的主要应用场景

Mode 目前在项目中主要用于以下四个维度：

### A. 动态 API 地址转发（最常用）

正如你所实践的，利用 `VITE_API_BASE_URL` 配合 Vite 的 `server.proxy`。

- **开发模式**：连 `localhost:3000`。
- **Staging 模式**：连测试服务器，方便与后端同事联调未上线的接口。

### B. 功能开关 (Feature Toggles)

你可以定义 `VITE_ENABLE_NEW_UI=true`。

- 在代码中通过 `if (import.meta.env.VITE_ENABLE_NEW_UI)` 来控制新功能是否对用户可见。
- 这允许你将未完成的功能代码合并进主分支，但在生产环境中将其彻底关闭。

### C. 条件式代码注入

有时候你只想在测试环境引入调试工具（如 `vConsole` 或 `eruda`）：

TypeScript

```
if (import.meta.env.MODE === 'staging') {
  import('vconsole').then(({ default: VConsole }) => new VConsole());
}
```

Vite 在打包时会识别这些条件。在 `production` 模式下，这段代码会被作为“死代码”彻底剔除（Tree Shaking），不会增加生产包的体积。

### D. 静态资源 CDN 路径

在 `project.json` 中，你可以为不同的模式配置不同的 `base` 路径：

- **Staging**: 放在服务器子目录下，如 `/test-app/`。
- **Production**: 使用 CDN 地址，如 `https://static.cdn.com/assets/`。

---

## 4. 在 Nx 环境下操作 Mode 的进阶技巧

由于你是在 Nx Monorepo 中使用，Mode 与 **Nx Configurations** 结合后会产生强大的联动效应：

1. 一键多端启动：
	
	你可以在 project.json 中配置一个名为 serve-all 的 target，它依赖于多个模式，从而实现“启动前端 Web 连接远程 Staging，同时启动后端 API 连接本地数据库”的复杂组合。
	
2. 缓存隔离：
	
	Nx 会将 Mode 作为缓存哈希（Hash）的一部分。这意味着当你运行 nx build web -c staging 后，再运行 nx build web -c production，Nx 会分别为它们建立缓存，互不干扰。

---

### 总结

Vite 的 Mode 是一个**声明式**的环境管理工具。它的出现让前端构建从“硬编码字符串”进化到了“配置驱动”。