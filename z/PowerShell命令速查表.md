---
tags:
  - tech/ops/windows
  - type/howto
  - status/growing
description: PowerShell 常用命令速查
created: 2025-09-29T11:51:59
updated: 2025-12-07T17:00:00
---

# PowerShell 命令速查表

**上级索引**：[[Resource]]

## 分屏快捷键

|**操作功能**|**快捷键**|**备注**|
|---|---|---|
|**左右拆分窗格**|`Alt` + `Shift` + `+`|将当前窗口垂直切开（左/右）|
|**上下拆分窗格**|`Alt` + `Shift` + `-`|将当前窗口水平切开（上/下）|
|**自动拆分窗格**|`Alt` + `Shift` + `D`|根据长宽比自动选择最优方向|
|**切换焦点 (移动)**|`Alt` + `方向键`|在各个分屏之间跳转|
|**调整分屏大小**|`Alt` + `Shift` + `方向键`|微调当前选中分屏的边缘|
|**关闭当前分屏**|`Ctrl` + `Shift` + `W`|只关闭当前选中的小窗口|
|**最大化分屏**|`无 (需在设置中绑定)`|将选中的分屏暂时填满整个窗口|

## 文件命令

| 命令                                                                                                                                |                    描述                    | 示例  |
| --------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------: | --- |
| `Compress-Archive -Path (Get-ChildItem -Path .\project -Force) -DestinationPath .\project_with_git.zip -CompressionLevel Optimal` |               完整压缩（包括隐藏文件）               |     |
| `(Get-ChildItem -Recurse -Force \| Measure-Object -Property Length -Sum).Sum / 1MB`                                               | 递归计算当前目录下所有文件的大小，并以 **MB** 为单位显示，保留两位小数。 |     |
|                                                                                                                                   |                                          |     |
