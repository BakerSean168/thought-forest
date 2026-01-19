---
tags:
  - tech/app/vscode
  - type/concept
  - status/growing
description: Turbo Console Log VSCode调试扩展
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VSCode MOC]] | [[Resource]]

---


## 简介

Turbo Console Log 是一个 VS Code 扩展，用于快速插入 `console.log` 语句。它可以自动生成带有变量名、函数名或表达式的日志，帮助调试 JavaScript/TypeScript 代码。

## 安装

1. 在 VS Code 中打开扩展市场（Ctrl+Shift+X）。
2. 搜索 "Turbo Console Log" 并安装。
3. 重启 VS Code。

## 基本用法

- **插入日志**：选中变量或表达式，按快捷键插入 `console.log`。
- **删除日志**：选中日志行，按快捷键删除所有相关日志。
- **注释日志**：选中日志行，按快捷键注释/取消注释。

## 快捷键（默认）

- **插入日志**：`Ctrl + Alt + L`（Windows/Linux）或 `Cmd+Alt+L`（Mac）。
- **删除所有日志**：`Ctrl+Alt+D`（Windows/Linux）或 `Cmd+Alt+D`（Mac）。
- **注释/取消注释日志**：`Ctrl+Alt+C`（Windows/Linux）或 `Cmd+Alt+C`（Mac）。

## 配置选项

在 VS Code 设置中搜索 "turbo-console-log" 可自定义：
- **日志前缀**：如 "🚀" 或自定义字符串。
- **日志格式**：如 `console.log('variable:', variable);`。
- **插入位置**：在选中行上方/下方插入。
- **支持语言**：默认 JavaScript/TypeScript，可扩展到其他。

## 示例

假设选中变量 `userName`：
- 插入后：`console.log('userName:', userName);`
- 在函数中：`console.log('functionName -> userName:', userName);`

更多详情请查看 [Turbo Console Log GitHub](https://github.com/Chakroun-Anas/turbo-console-log)。
