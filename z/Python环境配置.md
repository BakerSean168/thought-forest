---
tags:
  - tech/dev/programming
  - type/concept
  - status/seed
description: Python环境配置
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Python MOC]] | [[后端开发 MOC]]

---


# Python环境配置 

## windows（scoop）

### python 本体

```powershell
# 安装最新稳定版 Python（会自动添加到 PATH） 
scoop install python 

# 安装指定版本（如 Python 3.9） 
scoop install python39
```

### 多 Python 版本管理

```powershell
# 安装 pipenv（虚拟环境 + 依赖管理）
scoop install pipenv

# 安装 poetry（现代依赖管理工具，支持虚拟环境）
scoop install poetry

# 安装 pyenv-win（Python 版本管理器，配合 scoop 使用更灵活）
scoop install pyenv-win
```

#### pyenv 使用

```powershell
# 查看可安装的 Python 版本
pyenv install --list

# 安装指定版本
pyenv install 3.10.11

# 为当前项目设置本地版本
pyenv local 3.10.11

# 查看当前生效版本
pyenv version
```

