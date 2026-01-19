---
tags: [status/growing, type/concept]
description: base_template
created: 2025-12-18T21:39:30
updated: 2025-12-18T21:55:18
---

# One Flow 

## 概览

- **提出者：** **Adam Ruka**
- **提出背景：** 很多团队觉得 Gitflow 太复杂（维护 `master` 和 `develop` 两个长期分支很累，且容易合并冲突），但又不能用 GitHub Flow（因为需要按版本发布 App）。
- **起源：** 针对 Gitflow 的一种简化和修正。
- **核心逻辑：** 它保留了 Gitflow 的严谨性，但去掉了 `develop` 分支。
	- 所有新功能直接从 `master` 切出。
	- 发布时使用临时分支，发布完删除。
	- 最终效果是让 Gitflow 的历史记录更清晰，操作更简单。
- **适用场景：** 想要 Gitflow 的版本管理能力，但不想忍受其复杂度的团队。
- **流程：**
	1. **日常开发：** 所有功能分支直接从 `master` 切出，做完后合并回 `master`（这一点和 GitHub Flow 一样）。
	2. **发布时 (Release)：** 当要把 `master` 上的代码打包成 `v1.0` 时，从 `master` 切出一个 `release/v1.0` 分支。
		- 在这个分支上进行测试、修 Bug、生成版本号。
		- 发布完成后，将该分支合并回 `master` 并打 Tag，最后**删除**该 release 分支。
	3. **修线上 Bug (Hotfix)：** 发现线上 `v1.0` 有 Bug，基于 `v1.0` 的 Tag 切出 `hotfix` 分支，修完合并回 `master`。
- **优势：** 你不需要维护那个永远滞后、容易冲突的 `develop` 分支，Git 历史线非常清晰。