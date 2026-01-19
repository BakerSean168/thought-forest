---
tags:
  - status/growing
  - type/concept
  - tech/dev
description: 2011 年左右，为了适应 GitHub 自身的开发节奏（一天部署几十次），Scott Chacon 提出了这个极简的工作流。
created: 2025-12-18T17:53:38
updated: 2025-12-18T21:35:22
tool: git
---

# Github Flow 

## 概览

- **提出者：** **Scott Chacon** (GitHub 的联合创始人/CIO)
- **起源：** 2011 年左右，为了适应 GitHub 自身的开发节奏（一天部署几十次），Scott Chacon 提出了这个极简的工作流。
- **核心特点：**
    - **只有一个长期分支：** `master`（或 `main`），且必须永远处于“可部署”状态。
    - **分支即功能：** 开发新功能直接从 `master` 切出，开发完发起 Pull Request (PR)，Review 通过后合并回 `master` 并**立即部署**。
- **适用场景：** 持续部署（CD）的 Web 应用、SaaS 软件。
- **缺点：** 缺乏“环境”概念（如测试环境、预发环境），如果合并后发现 Bug，回滚生产环境会比较麻烦。

## 核心概念

这是目前最流行、也是最适合 `release-please` 的模式。你只需要两个核心概念：

1. **`main` 分支 (受保护的主干)**：
	- 永远是可以部署的状态。
	- **Release Please 只监听这个分支。**
	- 所有的版本发布 Tag 都在这里产生。
2. **`feat/xxx` 或 `fix/xxx` 分支 (临时分支)**：
	- 开发新功能时创建。
	- 开发完提 PR 合并回 `main`。
	- **不要**有一个长期的 `dev` 或 `develop` 分支。