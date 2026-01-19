---
tags:
  - tech/ops/git
  - type/howto
  - status/growing
description: gitflow插件（vscode)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---

# Git Flow 插件（VS Code）快速参考手册

本文档介绍 VS Code 中最流行的 `gitflow` 插件（作者：`vector-of-bool`）的功能和核心用法。

## 1. 概览（Overview）

* 插件功能：将 Git Flow 模型的复杂命令行操作，封装为 VS Code 命令面板指令。
* 适用场景：遵循 Git Flow 规范、需要严格版本控制和并行开发的团队。
* 一句话总结：自动化 Git Flow 分支操作，实现高效版本管理。

## 2. 关键概念（Core Concepts）

| 分支类型 | 作用 | 派生自 | 合并目标 |
|---|---|---|---|
| **`master` / `main`** | 生产环境稳定代码，只接收合并。 | N/A | N/A |
| **`develop`** | 集成开发分支，所有功能分支的基础。 | N/A | N/A |
| **Feature (功能)** | 开发新功能。 | `develop` | `develop` |
| **Release (发布)** | 准备新版本发布、最终 Bug 修复、打 Tag。 | `develop` | `master` & `develop` |
| **Hotfix (紧急修复)** | 修复生产环境 Bug。 | `master` / `main` | `master` & `develop` |

## 3. 安装与环境准备（Installation / Setup）

* 环境要求：VS Code、Git CLI（命令行工具）、Git 仓库。
* 安装步骤：扩展视图 (`Ctrl+Shift+X`) -> 搜索 `gitflow` -> 安装 (`vector-of-bool` 版本)。
* 配置修改：在 `settings.json` 中搜索 `gitflow.branch` 可自定义分支名称和前缀。

## 4. 快速开始（Quick Start）

所有操作均通过 **命令面板** (`Ctrl+Shift+P` / `Cmd+Shift+P`) 完成，输入 `Gitflow: ` 即可查找。

| 步骤 | 命令面板输入 | 示例操作 |
|---|---|---|
| **1. 初始化** | `Gitflow: Initialize repository for gitflow` | 接受默认设置即可。 |
| **2. 开始 Feature** | `Gitflow: Feature: start` | 创建并切换到 `feature/<name>`。 |
| **3. 完成 Feature** | `Gitflow: Feature: finish` | 合并回 `develop`，删除 `feature` 分支。 |
| **4. 开始 Release** | `Gitflow: Release: start` | 创建并切换到 `release/<version>`。 |
| **5. 完成 Release** | `Gitflow: Release: finish` | 合并到 `master` & `develop`，创建 Tag，删除 `release`。 |
| **6. Hotfix 流程** | `Gitflow: Hotfix: start` / `finish` | 流程与 Release 类似，但基于 `master`。 |

## 5. 进阶使用（Advanced Usage）

* **推荐配置 (`settings.json`)**：
  * `"gitflow.feature.finish.rebase"`: `true` (使用 rebase 模式合并，保持历史线性，**推荐**)。
* **常见模式**：
  * **Rebase 优先:** 在 `finish` 之前先执行 `Gitflow: Feature: rebase`，确保基于最新的 `develop`。
  * **远程同步:** 插件只处理本地分支，完成后需**手动执行 `git push/pull`** 同步到远程仓库。

## 6. 常见问题（FAQ）

| 问题 (Q) | 解决方法 (A) |
|---|---|
| **Q1: 命令面板无反应？** | **A:** 检查工作区是否为有效的 Git Flow 仓库（是否已 `Initialize`）。 |
| **Q2: `finish` 提示冲突？** | **A:** 插件无法自动解决，需手动解决冲突并提交 Commit 后，再尝试 `finish`。 |
| **Q3: 报错“未提交的修改”？** | **A:** 请先使用 VS Code Git 面板或 CLI 提交或暂存所有本地修改。 |

## 7. 最佳实践（Best Practices）

1. **Work Flow:** Feature 流程应为 **Start → Commit & Rebase → Finish**。
2. **别忘了 Push:** `finish` 操作完成后，务必手动 Push `develop` 和 `master` 分支及 Tag 到远程仓库。
3. **调试:** 遇到问题时，查看 VS Code 底部栏 **Output 面板** 中 **`Git Flow`** 的输出，了解后台执行的具体 Git 命令。

## 8. 资源与参考（Resources）

* **官方 GitHub:** [vector-of-bool/vscode-gitflow](https://github.com/vector-of-bool/vscode-gitflow)
* **后续学习:** 深入了解 Git Flow 模型（`Rebase` vs `Merge`）。

---

希望这次您可以顺利复制这些内容！
