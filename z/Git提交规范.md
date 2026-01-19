---
tags:
  - tech/ops/git
  - type/concept
  - status/seed
description: Git提交规范
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# Git提交规范 

## 1. 提交规范

使用约定式提交（Conventional Commits）：

```bash
 格式：<type>[optional scope]: <description>
git commit -m "feat(auth): add login functionality"
git commit -m "fix(ui): resolve button alignment issue"
git commit -m "docs: update API documentation"
```

**常用类型：**

- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式调整
- `refactor`: 代码重构
- `test`: 测试相关
- `chore`: 构建过程或辅助工具的变动

## 2. 分支命名规范

```bash
 功能分支
feature/user-authentication
feature/payment-integration

 修复分支
fix/header-layout-bug
hotfix/critical-security-patch

 发布分支
release/v1.2.0
```

## 3. .gitignore 配置

```bash
 依赖目录
node_modules/
npm-debug.log*

 构建输出
dist/
build/
.nuxt/

 环境配置
.env
.env.local
.env.production

 IDE 配置
.vscode/
.idea/

 缓存文件
.cache/
.parcel-cache/
```
