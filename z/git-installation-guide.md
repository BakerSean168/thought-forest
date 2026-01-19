---
tags:
  - type/howto
  - status/evergreen
description: 如何xxx
---


# git-installation-guide 

## scoop

可以用 uniGetUI，搜索出来下载

关键是如下信息：
```powershell
# Scoop 在安装完 Git 后给出了三条建议，主要是为了增强你在 Windows 上的使用体验：

# **凭据管理器 (Credential Manager):**
> `Set Git Credential Manager Core by running: "git config --global credential.helper manager"`

# **含义：** 建议你运行这条命令。它的作用是让 Windows 帮你**记住 GitHub 或 Gitee 的账号密码**。下次你推送（push）代码时，就不需要反复输入密码了。

# **右键菜单 (Context Menu):**
> `To add context menu entries, run 'D:\scoop\apps\git\current\install-context.reg'`

# *含义：** 如果你希望在文件夹上点击**右键**时看到“Git Bash Here”或“Git GUI Here”，请去这个路径双击运行那个 `.reg` 注册表文件。

# *文件关联 (File Associations):**
> `To create file-associations for .git* and .sh files, run 'D:\scoop\apps\git\current\install-file-associations.reg'`

# *含义：** 运行这个文件后，电脑会把 `.sh`（脚本文件）等与 Git 相关的文件关联起来，让你双击就能运行。
```