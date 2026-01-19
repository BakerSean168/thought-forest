---
tags:
  - tech/ops/windows
  - type/howto
  - status/growing
description: winget常用命令速查表
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Resource]] | [[Resource]]

---


# winget常用命令速查表

## 命令

| 命令用途                | 命令示例                                  | 说明                                                                 |
|-------------------------|-------------------------------------------|----------------------------------------------------------------------|
| **搜索软件**            | `winget search <关键词>`                  | 搜索匹配关键词的软件，如 `winget search vscode`                      |
| **精准搜索（按ID）**    | `winget search --id <软件ID>`             | 按官方ID搜索（避免重名），如 `winget search --id Microsoft.PowerShell` |
| **安装软件**            | `winget install <软件ID/名称>`            | 安装指定软件，如 `winget install Microsoft.VisualStudioCode`          |
| **安装时忽略提示**      | `winget install <软件ID> --silent`        | 静默安装（部分软件支持，无需手动点击下一步）                         |
| **更新所有软件**        | `winget upgrade --all`                    | 升级系统中所有可通过 winget 更新的软件                               |
| **更新指定软件**        | `winget upgrade <软件ID/名称>`            | 升级单个软件，如 `winget upgrade Google.Chrome`                       |
| **卸载软件**            | `winget uninstall <软件ID/名称>`          | 卸载指定软件，如 `winget uninstall 7zip.7zip`                        |
| **查看已安装软件**      | `winget list`                             | 列出系统中所有可被 winget 识别的已安装软件                           |
| **查看软件详情**        | `winget show <软件ID/名称>`               | 显示软件的版本、发布者、下载链接等信息，如 `winget show Node.js`      |
| **检查 winget 版本**    | `winget --version`                        | 查看当前 winget 的版本号                                             |
| **更新 winget 自身**    | `winget upgrade --id Microsoft.Winget.Source` | 升级 winget 包管理器本身（依赖 Microsoft Store 或官方源）            |
| **添加软件源**          | `winget source add --name <源名称> <源URL>` | 添加自定义软件仓库（较少用，默认源已覆盖主流软件）                   |
| **刷新软件源缓存**      | `winget source update`                    | 更新本地软件源缓存，确保获取最新软件列表                             |
