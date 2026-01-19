---
tags:
  - tech/lang/nodejs
  - type/journal
  - status/seed
description: Nx Monorepo教程demo实践记录
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Nx MOC]] | [[前端工程化 MOC]]

---


# Nx的教程demo尝试过程 

## 创建 nx 项目

教程中是一键创建了一个

```bash
Directoryacme/
	Directory.github/
		Directoryworkflows/
		ci.yml
	Directory.nx/
		...
	Directorypackages/
		.gitkeep
	README.md
	.prettierignore
	.prettierrc
	nx.json
	package-lock.json
	package.json
	tsconfig.base.json
	tsconfig.json
```

- `.nx`： 本地 workspace 元数据
- `nx.json`： 配置信息和全局默认配置（子项目能继承）

### 创建子项目

这也是 Nx 的关键，demo 中使用了 Nx 的脚手架命令创建了 zoo 和 animal 包（zoo 依赖 animal）。
此时可以直接在 zoo 的 package 中引入 animal 包
```json
// packages/zoo/package.json
{  "dependencies": {    "@acme/animal": "*"  }}
```

然后可以通过 `npm install` 来安装。


> [!NOTE] **最重要的是**
> 当使用 `npx nx build zoo` 时，能够自动地构建 animal 包（zoo 的依赖），然后构建 zoo。


### nx 命令

![[Pasted image 20251027160654.png]]

## 经验总结

Nx 主要是通过 nx 的命令执行，来实现依赖分析（自动构建相关库）和缓存管理（构建时直接引用缓存）

有多种插件可以扩展功能

有 Nx-Cloud 可以提升构建速度
## 信息参考

[Building and Testing TypeScript Packages in Nx | Nx](https://nx.dev/docs/getting-started/tutorials/typescript-packages-tutorial)
