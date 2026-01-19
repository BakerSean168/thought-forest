---
tags:
  - tech/lang/nodejs
  - tech/ops/monorepo
  - type/concept
  - status/growing
description: Turborepo - 高性能 Monorepo 构建系统
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端工程化 MOC]] | [[Node.js MOC]]

---

# Turborepo 

Turborepo 是由 Vercel 维护的高性能 Monorepo 构建系统，专注于 JavaScript/TypeScript 生态。

## 基础概念

### 核心特性

| 特性 | 说明 |
|------|------|
| **增量构建** | 只重新构建发生变化的包 |
| **远程缓存** | 跨 CI/CD 和团队成员共享构建缓存 |
| **并行执行** | 自动识别依赖关系，最大化并行 |
| **任务管道** | 声明式任务依赖配置 |
| **零配置** | 开箱即用，渐进式配置 |

### 与其他工具对比

| 对比项 | Turborepo | Nx | Lerna |
|--------|-----------|-----|-------|
| 配置复杂度 | 低 | 高 | 中 |
| 远程缓存 | ✅ | ✅ (付费) | ❌ |
| 构建速度 | 快 | 快 | 慢 |
| 学习曲线 | 低 | 高 | 低 |
| 生态集成 | Vercel | 独立 | npm |

### 工作原理

1. **任务哈希**：根据输入文件计算哈希
2. **缓存命中**：相同哈希直接使用缓存结果
3. **任务图**：分析包间依赖，确定执行顺序
4. **并行执行**：无依赖关系的任务并行运行

## 使用指南

### 安装

```bash
# 全局安装
npm install turbo -g

# 项目安装
pnpm add turbo -D -w
```

### 基础配置

在项目根目录创建 `turbo.json`：

```json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": []
    },
    "lint": {
      "outputs": []
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

### 常用命令

```bash
# 运行所有包的 build 任务
turbo build

# 运行特定包的任务
turbo build --filter=@myorg/web

# 运行所有任务（并行）
turbo build test lint

# 开发模式
turbo dev

# 查看任务图
turbo build --graph
```

### package.json 配置

```json
{
  "scripts": {
    "build": "turbo build",
    "dev": "turbo dev",
    "test": "turbo test",
    "lint": "turbo lint"
  }
}
```

### 过滤器语法

```bash
# 指定包
turbo build --filter=@myorg/ui

# 包及其依赖
turbo build --filter=@myorg/web...

# 只有依赖
turbo build --filter=...@myorg/web

# Git 变更的包
turbo build --filter=[HEAD^1]

# 组合过滤
turbo build --filter=./apps/* --filter=!@myorg/docs
```

## 实战经验

### 远程缓存配置

```bash
# 登录 Vercel
npx turbo login

# 链接项目
npx turbo link
```

或自托管远程缓存：

```json
{
  "remoteCache": {
    "enabled": true,
    "teamId": "team_xxx",
    "signature": true
  }
}
```

### 最佳实践

1. **合理划分任务**：细粒度任务更利于缓存命中
2. **正确声明输出**：`outputs` 要精确匹配实际产物
3. **使用 `dependsOn: ["^build"]`**：表示依赖先构建
4. **开发任务禁用缓存**：`"cache": false, "persistent": true`

### 常见问题

**Q: 缓存不生效？**
- 检查 `outputs` 配置是否正确
- 使用 `turbo build --dry` 查看任务哈希
- 确认环境变量是否影响哈希

**Q: 任务顺序错误？**
- 检查 `dependsOn` 配置
- `^` 前缀表示依赖包的任务
- 无前缀表示同包的任务

## 参考资料

- **官方文档**：[turbo.build/docs](https://turbo.build/docs)
- **中文文档**：[turbo.net.cn/docs](https://turbo.net.cn/docs)
- **相关笔记**：[[Monorepo 架构]] | [[Nx-Overview]]
