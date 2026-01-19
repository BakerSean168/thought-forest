---
tags:
  - type/howto
  - status/evergreen
description: 如何xxx
---


# 更改Scoop默认安装位置 

这样下次重装系统，只要把环境变量指回去，大部分软件甚至不用重装。

在安装 Scoop 之前配置（最推荐）

```powershell
# 设置用户软件安装到 D:\Scoop
$env:SCOOP='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

# 设置全局软件安装到 D:\ScoopGlobal (需管理员权限)
$env:SCOOP_GLOBAL='D:\ScoopGlobal'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')

# 然后再执行官方的安装命令
irm get.scoop.sh | iex
```