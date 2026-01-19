---
tags:
  - tech/dev/frontend/eng
  - type/moc
description: 依赖管理（包管理、版本策略、镜像与私服）入口 MOC
created: 2025-12-11T13:45:00
updated: 2025-12-11T13:45:00
---

> 上级索引: [[035_Dev]] | [[Development Environment MOC]]

# Dependency Management MOC

本页为前端项目的依赖管理入口（包管理、版本策略、registry/镜像、私服与依赖排查）。

## 快速入口

- 安装与镜像：[[Quartz 4.0 静态部署指南]]
- package manager: [[pnpm]] / [[npm]]（仓库中示例：[[AG-Grid]]）
- 私服与镜像配置：[[Electron 项目问题]]

## 📂 相关笔记（手工索引）



- [[AG-Grid]] — 使用 pnpm/npm/yarn 安装示例
- [[Quartz 4.0 静态部署指南]] — 包与构建部署示例（含 GitHub Actions）
- [[agent-tars]] — 使用 pnpm 的示例



> 说明：此页面为导航入口，深度的依赖策略与私服运维请放在子笔记并在其 frontmatter 中添加 `moc/parent: "Dependency Management MOC"`。

## 依赖的管理架构的实践设计（应该是一些比较好的实践方法

- [[monorepo(多层级）项目中的依赖管理]]


## Debug

- [[nodejs依赖冲突问题-node]]
- [[包废弃与peer问题]]