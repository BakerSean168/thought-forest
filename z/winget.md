---
tags:
  - tech/ops/windows
  - type/concept
  - status/growing
description: winget Windows包管理器
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Windows]] | [[Resource]]

---


# winget （Windows 包管理器）

## 基础概念

- **官方背景**：由微软开发并内置在 Windows 11 及 Windows 10 后期版本中（可通过 Microsoft Store 或官网单独安装），是微软官方推出的包管理工具。
- **软件来源**：主要聚焦于主流商业软件和桌面应用（如 Chrome、VS Code、7-Zip 等），软件包通常为官方发布的安装程序（.exe、.msi）。
- **安装方式**：默认以系统级权限安装（类似常规软件安装到 `Program Files` 目录），适合大多数用户的日常软件需求。
- **特点**：操作简单，与系统集成度高，支持通过 `winget search`、`winget install`、`winget upgrade` 等命令快速管理软件。

## 使用指南

## 实战经验

### 下载 powershell

#### 1. 搜索项目是否存在

```powershell
PS C:\Users\Alexander> winget search powershell
名称                                 ID                                          版本          匹配            源
----------------------------------------------------------------------------------------------------------------------
PowerShell                           9MZ1SNWT0N5D                                Unknown                       msstore
PowerShell Preview                   9P95ZZKTNRN4                                Unknown                       msstore
PowerShell to exe&msi Converter free XPDCHZH119SRT8                              Unknown                       msstore
PowerShell                           Microsoft.PowerShell                        7.5.3.0                       winget
```

#### 2. 使用 ID 精确下载

```powershell
PS C:\Users\Alexander> winget install Microsoft.PowerShell
找到已安装的现有包。正在尝试升级已安装的包...
找不到可用的升级。
配置的源中没有可用的较新的包版本。
```


## 经验总结

|**任务类别**|**目的**|**命令示例**|**描述**|
|---|---|---|---|
|**查找/搜索**|搜索仓库中可用的应用程序包。|`winget search <关键词>`|通过名称、ID 或标签查找应用，例如：`winget search Chrome`。|
|**安装**|安装指定的应用程序。|`winget install <ID>`|使用 ID 安装应用，例如：`winget install Microsoft.VisualStudioCode`。|
|**导出/导入**|**生成和恢复应用程序清单。**|`winget export -o apps.json`|导出当前系统上可识别的应用到 JSON 文件（您的“软件蓝图”）。|
|||`winget import -i apps.json`|读取 JSON 文件，在新系统上自动安装清单中的所有应用。|
|**管理**|列出当前系统上所有可被 `winget` 识别和管理的应用程序。|`winget list`|查看已安装软件的列表。|
||检查并升级所有可升级的应用程序。|`winget upgrade --all`|一键更新所有 `winget` 管理的软件。|
||卸载指定的应用程序。|`winget uninstall <ID>`|卸载应用程序。|
|**配置**|管理 `winget` 的软件源（仓库）。|`winget source list`|查看所有已配置的源（例如 `winget` 和 `msstore`）。|

## 信息参考

