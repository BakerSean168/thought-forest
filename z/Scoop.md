---
tags:
  - tech/ops/windows
  - type/howto
  - status/growing
description: Scoop包管理器完整指南
created: 2025-01-01T00:00:00
updated: 2025-12-15T22:04:27
---

> [!info] **上级索引**
> [[Resource]] | [[Windows]]

---

# Scoop 包管理器完整指南

## 目录

1. [基础概念](#基础概念)
2. [使用指南](#使用指南)
3. [实战经验](#实战经验)
4. [信息参考](#信息参考)

---

## 基础概念

### 什么是 Scoop？

Scoop 是一个 **命令行应用程序安装器**，专为 Windows 设计。它简化了软件安装过程，类似于 Linux 的 apt/yum 或 macOS 的 Homebrew。

**核心特性：**
- 🎯 **便捷安装**：一行命令安装应用程序
- 📦 **版本管理**：轻松切换和管理多个版本
- 🔒 **依赖管理**：自动处理应用依赖
- 🌐 **开源生态**：社区维护的 bucket 库
- 💻 **便携性**：支持便携式应用程序
- 🔄 **自动更新**：一键更新所有已安装应用
- 🚀 **快速启动**：应用程序自动添加到 PATH

### Scoop 架构

```
Scoop
├── Core（核心）
│   ├── 安装器
│   ├── 包管理逻辑
│   └── 命令行界面
│
├── Buckets（仓库）
│   ├── main（官方仓库）
│   ├── extras（扩展仓库）
│   ├── versions（版本仓库）
│   ├── nerd-fonts（字体）
│   ├── java（Java 工具链）
│   └── community buckets（社区仓库）
│
└── Apps（应用）
    ├── 清单文件（manifest/JSON）
    ├── 下载 URL
    ├── 依赖关系
    └── 配置信息
```

### 关键概念

| 术语         | 定义                                 | 示例                     |
| ------------ | ------------------------------------ | ------------------------ |
| **Bucket**   | 包含应用清单的仓库                   | `main`、`extras`、`java` |
| **Manifest** | 描述应用信息的 JSON 文件             | 版本、下载地址、依赖     |
| **App**      | 通过 Scoop 管理的应用程序            | `git`、`node`、`vscode`  |
| **Shim**     | 轻量级 EXE 包装器，将应用暴露给 PATH | 使 `git.exe` 可全局调用  |
| **Update**   | 更新应用到最新版本                   | `scoop update app-name`  |

### Scoop 工作流程

```
1. 定义 Bucket
   ↓
2. 浏览可用应用
   ↓
3. 安装应用（Scoop 下载并配置）
   ↓
4. 应用自动添加到 PATH
   ↓
5. 在命令行或应用菜单使用
   ↓
6. 管理版本、卸载或更新
```

### 与其他包管理器对比

| 特性         | Scoop                    | Chocolatey         | winget         |
| ------------ | ------------------------ | ------------------ | -------------- |
| **安装位置** | 用户级（通常 `~/scoop`） | 系统级（需管理员） | 用户级或系统级 |
| **权限需求** | 无需管理员               | 需要管理员         | 需要管理员     |
| **编程语言** | PowerShell               | PowerShell         | C++            |
| **官方支持** | 社区维护                 | 商业支持           | 官方维护       |
| **学习曲线** | 简单                     | 中等               | 简单           |
| **应用数量** | ~10,000                  | ~9,000             | ~5,000         |

---

## 使用指南

### 前置要求

#### 1. 系统要求

- **操作系统**：Windows 7 SP1 及以上
- **PowerShell**：版本 5.0 及以上（Windows 10 已预装）
- **磁盘空间**：至少 100 MB
- **网络**：稳定的互联网连接

#### 2. 检查 PowerShell 版本

```powershell
$PSVersionTable.PSVersion
```

应显示 5.0 或更高版本。如版本过低，可从 [GitHub Releases](https://github.com/PowerShell/PowerShell/releases) 下载最新版本。

#### 3. 设置执行策略

```powershell
# 允许本地脚本执行
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```

### 安装 Scoop

#### 方式一：标准安装（推荐）

```powershell
# 在 PowerShell 中运行
iwr -useb get.scoop.sh | iex
```

#### 方式二：自定义安装位置

```powershell
# 指定安装目录（例如：D:\scoop）
$env:SCOOP = 'D:\scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'CurrentUser')

# 执行安装
iwr -useb get.scoop.sh | iex
```

#### 方式三：手动安装

```powershell
# 克隆官方仓库
git clone https://github.com/ScoopInstaller/Scoop.git

# 进入目录并运行安装脚本
cd Scoop
.\install.ps1
```

#### 验证安装

```powershell
scoop --version
scoop help
```

### 常用命令

#### 搜索和浏览

```powershell
# 搜索应用
scoop search node
scoop search "code editor"

# 查看应用信息
scoop info git
scoop show nodejs

# 列出可用 bucket
scoop bucket list

# 查看已安装应用
scoop list
```

#### 安装和卸载

```powershell
# 安装单个应用
scoop install git
scoop install nodejs

# 安装多个应用
scoop install git nodejs python vscode

# 从特定 bucket 安装
scoop install extras/vscode

# 卸载应用
scoop uninstall git

# 卸载并删除数据
scoop uninstall -p git  # -p 表示 purge（清除）

# 卸载所有应用
scoop uninstall -a
```

#### 版本管理

```powershell
# 查看可用版本
scoop list -v  # 列出已安装版本

# 安装特定版本
scoop install git@2.40.0

# 切换版本（重置）
scoop reset git@2.40.0

# 卸载特定版本
scoop uninstall git@2.40.0

# 保留多个版本
scoop install git@2.40.0
scoop install git@2.41.0
# 然后在两个版本间切换
```

#### 更新

```powershell
# 更新 Scoop 本身
scoop update

# 检查可更新的应用
scoop status

# 更新单个应用
scoop update git

# 更新所有应用
scoop update *

# 强制更新（即使版本相同）
scoop update git -f
```

#### Bucket 管理

```powershell
# 添加 bucket
scoop bucket add main           # 官方主仓库（通常默认）
scoop bucket add extras         # 额外应用
scoop bucket add versions       # 版本管理仓库
scoop bucket add nerd-fonts     # Nerd 字体库
scoop bucket add java           # Java 工具链
scoop bucket add nonportable    # 非便携应用

# 添加自定义 bucket
scoop bucket add my-bucket https://github.com/username/my-bucket.git

# 列出所有 bucket
scoop bucket list

# 移除 bucket
scoop bucket rm extras

# 查看 bucket 信息
scoop bucket known
```

#### 清理和维护

```powershell
# 清理缓存
scoop cache show     # 显示缓存大小
scoop cache rm       # 清理所有缓存
scoop cache rm git   # 清理特定应用缓存

# 显示垃圾文件（旧版本）
scoop cleanup -g

# 清理所有已卸载应用的旧版本
scoop cleanup *

# 清理特定应用的旧版本
scoop cleanup git

# 诊断问题
scoop checkup       # 检查常见问题
scoop diagnose      # 显示诊断信息
```

#### 其他有用命令

```powershell
# 导出已安装应用列表
scoop export > apps.txt

# 从文件导入（批量安装）
scoop import apps.txt

# 创建应用快捷方式
scoop shim nodejs

# 重置应用（清除配置，重新初始化）
scoop reset git

# 卸载应用所有版本
scoop uninstall git -g
```

### 配置 Scoop

Scoop 配置文件位置：`~\scoop\config.json`

常见配置项：

```json
{
  "proxy": "[proxy server]",
  "use_lessmsi": true,
  "use_7zip": true,
  "cache": "$env:USERPROFILE\\AppData\\Local\\Scoop\\cache",
  "last_update": "2025-10-20T10:00:00.000Z",
  "aria2-enabled": true,
  "aria2-min-split-size": 5242880,
  "aria2-max-concurrent-downloads": 5,
  "show_update_log": true
}
```

**配置示例：**

```powershell
# 设置代理
scoop config proxy 127.0.0.1:1080

# 启用 Aria2 加速下载
scoop config aria2-enabled true
scoop config aria2-max-concurrent-downloads 32

# 设置缓存目录
scoop config cache D:\scoop-cache

# 查看当前配置
scoop config

# 重置配置
scoop config --remove <key>
```

---

## 实战经验

[[更改Scoop默认安装位置]]

### 场景一：开发环境快速配置

#### 目标：5 分钟配置完整前端开发环境

```powershell
# 1. 安装 Scoop
iwr -useb get.scoop.sh | iex

# 2. 添加必要 bucket
scoop bucket add extras
scoop bucket add nerd-fonts

# 3. 安装开发工具链
scoop install git nodejs python vscode

# 4. 安装开发必备应用
scoop install extras/oh-my-posh
scoop install extras/nerd-fonts-FiraCode
scoop install extras/postman
scoop install extras/dbeaver

# 5. 验证
node --version
npm --version
python --version
```

**预期结果：** 一套完整的前端开发环境就绪，所有工具均可在 PowerShell 中直接调用。

### 场景二：版本管理和切换

#### 需求：同时使用 Node.js 18 和 20

```powershell
# 1. 安装多个版本
scoop install versions/nodejs@18.20.0
scoop install versions/nodejs@20.10.0

# 2. 查看已安装版本
scoop list -v

# 3. 切换版本
scoop reset nodejs@18.20.0  # 切换到 18.20.0
node --version              # v18.20.0

# 4. 切换到另一版本
scoop reset nodejs@20.10.0  # 切换到 20.10.0
node --version              # v20.10.0
```

**技巧：** 使用 `nvm` 或 `fnm`（Fast Node Manager）管理 Node 版本更便捷：

```powershell
scoop install fnm
fnm install 18
fnm install 20
fnm use 18
```

### 场景三：备份和恢复开发环境

#### 备份当前环境

```powershell
# 导出已安装应用列表
scoop export > C:\backup\apps-$(Get-Date -Format 'yyyyMMdd').txt

# 备份 Scoop 配置
Copy-Item $env:USERPROFILE\scoop\config.json -Destination C:\backup\
Copy-Item $env:USERPROFILE\scoop\buckets -Destination C:\backup\ -Recurse
```

#### 恢复环境

```powershell
# 在新机器上安装 Scoop
iwr -useb get.scoop.sh | iex

# 恢复 bucket
Copy-Item C:\backup\buckets -Destination $env:USERPROFILE\scoop\ -Recurse

# 批量安装应用
scoop import C:\backup\apps-20251020.txt
```

### 场景四：国内加速（代理和镜像）

#### 问题：GitHub 下载速度慢

```powershell
# 方案一：配置代理
scoop config proxy 127.0.0.1:1080  # 本地 Clash/V2Ray 代理

# 方案二：启用 Aria2 多线程下载
scoop config aria2-enabled true
scoop config aria2-max-concurrent-downloads 32
scoop install aria2

# 方案三：修改 hosts（不推荐）
# 编辑 C:\Windows\System32\drivers\etc\hosts
# 添加 GitHub 镜像源 IP

# 方案四：使用国内镜像 bucket
scoop bucket add mirror-main https://gitee.com/scoop-installer/Main
scoop bucket add mirror-extras https://gitee.com/scoop-installer/Extras
```

### 场景五：便携化应用

#### 创建便携应用包

```powershell
# 安装便携版应用
scoop install extras/vscode-portable

# 导出应用（包含所有依赖）
scoop export > apps.txt

# 在另一台机器导入（无需网络即可使用）
scoop import apps.txt
```

### 常见问题排查

#### Q1：`scoop` 命令不识别

**原因：** 环境变量未正确配置

```powershell
# 检查 Scoop 安装路径
$env:SCOOP

# 重新配置环境变量
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'CurrentUser')

# 重启 PowerShell
```

#### Q2：下载速度慢或中断

**解决方案：**

```powershell
# 启用 Aria2
scoop install aria2
scoop config aria2-enabled true

# 或配置代理
scoop config proxy <proxy-server>

# 清理缓存后重试
scoop cache rm *
scoop install app-name -f
```

#### Q3：某应用无法启动或找不到

```powershell
# 重置应用
scoop reset app-name

# 检查依赖
scoop diagnose

# 重新安装
scoop uninstall app-name
scoop install app-name
```

#### Q4：磁盘空间不足

```powershell
# 显示缓存大小
scoop cache show

# 清理缓存
scoop cache rm *

# 清理已卸载应用的旧版本
scoop cleanup *

# 或指定清理特定应用
scoop cleanup git
```

### 性能优化建议

1. **启用 Aria2 加速下载**

   ```powershell
   scoop install aria2
   scoop config aria2-enabled true
   scoop config aria2-max-concurrent-downloads 16
   ```

2. **使用 SSD 存储 Scoop**

   ```powershell
   # 将 Scoop 放在 SSD 上，避免系统盘压力
   $env:SCOOP = 'D:\scoop'
   ```

3. **定期清理缓存**

   ```powershell
   scoop cache rm *
   scoop cleanup *
   ```

4. **使用本地 bucket 镜像**
   - 国内用户可使用 Gitee 镜像加速

---

## 信息参考

### 官方资源

| 资源         | 链接                                                |
| ------------ | --------------------------------------------------- |
| **主页**     | https://scoop.sh/                                   |
| **GitHub**   | https://github.com/ScoopInstaller                   |
| **官方文档** | https://github.com/ScoopInstaller/Scoop/wiki        |
| **问题反馈** | https://github.com/ScoopInstaller/Scoop/issues      |
| **讨论论坛** | https://github.com/ScoopInstaller/Scoop/discussions |

### 常用 Bucket 列表

| Bucket          | 内容         | 添加命令                                                   |
| --------------- | ------------ | ---------------------------------------------------------- |
| **main**        | 官方核心应用 | `scoop bucket add main`（默认）                            |
| **extras**      | 额外应用     | `scoop bucket add extras`                                  |
| **versions**    | 多版本支持   | `scoop bucket add versions`                                |
| **nerd-fonts**  | Nerd 字体库  | `scoop bucket add nerd-fonts`                              |
| **java**        | Java 工具链  | `scoop bucket add java`                                    |
| **nonportable** | 非便携应用   | `scoop bucket add nonportable`                             |
| **nightlies**   | 夜间构建     | `scoop bucket add nightlies`                               |
| **dorado**      | 额外工具     | `scoop bucket add dorado https://github.com/h404bi/dorado` |

### 热门应用速查表

#### 开发工具

```powershell
# 版本控制
scoop install git

# 编程语言
scoop install python
scoop install nodejs
scoop install go
scoop install rust

# 编辑器/IDE
scoop install vscode
scoop install sublime-text
scoop install extras/jetbrains-toolbox

# 数据库
scoop install postgresql
scoop install mysql
scoop install sqlite

# 容器化
scoop install docker
scoop install docker-compose
```

#### 生产力工具

```powershell
# 终端增强
scoop install oh-my-posh
scoop install starship
scoop install posh-git

# 文件管理
scoop install extras/everything
scoop install 7zip
scoop install winrar

# 网络工具
scoop install curl
scoop install wget
scoop install postman

# 媒体工具
scoop install ffmpeg
scoop install imagemagick
```

#### 字体和美化

```powershell
# Nerd 字体
scoop bucket add nerd-fonts
scoop install nerd-fonts-FiraCode
scoop install nerd-fonts-JetBrainsMono

# 主题
scoop install extras/oh-my-posh

# 终端
scoop install windows-terminal
```

### 常用快捷命令

```powershell
# 创建快捷别名（添加到 $PROFILE）
Set-Alias -Name ss -Value 'scoop search'
Set-Alias -Name si -Value 'scoop install'
Set-Alias -Name su -Value 'scoop uninstall'
Set-Alias -Name sl -Value 'scoop list'
Set-Alias -Name sup -Value 'scoop update'

# 使用示例
ss node
si git nodejs
su git
sl
sup *
```

### 脚本示例

#### 批量安装脚本

```powershell
# install-dev-env.ps1
# 开发环境快速配置脚本

# 添加 bucket
scoop bucket add extras
scoop bucket add versions
scoop bucket add nerd-fonts

# 安装开发工具
$apps = @(
    'git',
    'nodejs',
    'python',
    'vscode',
    'extras/oh-my-posh',
    'extras/postman',
    'extras/dbeaver',
    'nerd-fonts-FiraCode'
)

foreach ($app in $apps) {
    Write-Host "Installing $app..." -ForegroundColor Cyan
    scoop install $app
}

Write-Host "Done!" -ForegroundColor Green
scoop list
```

#### 自动更新脚本

```powershell
# auto-update.ps1
# 定期更新所有应用

scoop update
scoop update *

# 清理缓存
scoop cache rm *

# 显示更新摘要
scoop status
```

### PowerShell Profile 配置

编辑 `$PROFILE` 文件添加以下内容：

```powershell
# ~/.profile (或 C:\Users\<User>\Documents\PowerShell\profile.ps1)

# 导入 Posh-Git
Import-Module posh-git

# 初始化 Oh-My-Posh
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH/agnoster.omp.json" | Out-String | Invoke-Expression

# Scoop 快捷别名
Set-Alias -Name ss -Value 'scoop search'
Set-Alias -Name si -Value 'scoop install'
Set-Alias -Name su -Value 'scoop uninstall'
Set-Alias -Name sl -Value 'scoop list'
Set-Alias -Name sup -Value 'scoop update'
Set-Alias -Name sci -Value 'scoop cache rm'

# 自定义函数
function scoop-backup {
    $timestamp = Get-Date -Format 'yyyyMMdd-HHmmss'
    scoop export > "C:\backup\scoop-apps-$timestamp.txt"
    Write-Host "✓ Backup created: scoop-apps-$timestamp.txt" -ForegroundColor Green
}
```

### 常见应用清单

**前端开发堆栈：**

```powershell
scoop install git nodejs pnpm vscode
scoop bucket add extras
scoop install extras/postman extras/dbeaver
```

**全栈开发堆栈：**

```powershell
scoop install git nodejs python
scoop bucket add java extras
scoop install java/jdk21 extras/vscode
```

**数据科学堆栈：**

```powershell
scoop install python
pip install jupyter pandas numpy scipy matplotlib
scoop install extras/anaconda  # 可选
```

### 故障排除资源

- **官方 Wiki**: https://github.com/ScoopInstaller/Scoop/wiki
- **常见问题**: https://github.com/ScoopInstaller/Scoop/wiki/FAQ
- **故障排除**: https://github.com/ScoopInstaller/Scoop/wiki/Troubleshooting
- **GitHub Issues**: https://github.com/ScoopInstaller/Scoop/issues

### 相关工具生态

- **fnm** - Fast Node Manager（Node 版本管理）
- **rbenv** - Ruby 版本管理
- **pyenv** - Python 版本管理
- **nvm-windows** - Node 版本管理器
- **direnv** - 环境变量管理

---

|**任务类别**|**目的**|**命令示例**|**描述**|
|---|---|---|---|
|**基本安装**|安装一个软件包。|`scoop install <名称>`|例如：`scoop install git` 或 `scoop install nodejs`。|
|**查找/搜索**|搜索仓库中可用的软件包。|`scoop search <关键词>`|搜索可用的工具和应用。|
|**导出/导入**|**生成和恢复开发环境清单。**|`scoop export > dev_apps.txt`|导出所有通过 `scoop` 安装的软件列表（您的“开发者蓝图”）。|
|||`scoop import dev_apps.txt`|读取 TXT 文件，在新系统上自动安装所有列出的软件包。|
|**管理**|列出所有已通过 `scoop` 安装的软件。|`scoop list`|查看 `scoop` 生态中的所有工具。|
||更新指定的或所有已安装的软件包。|`scoop update <名称>` / `scoop update *`|更新软件到最新版本。|
||卸载指定的软件包。|`scoop uninstall <名称>`|卸载软件并清理相关文件。|
|**仓库管理**|添加额外的仓库（如 bucket）。|`scoop bucket add extras`|添加社区维护的额外软件源，以获取更多应用。|

## 总结

Scoop 是 Windows 开发者的强大工具，可显著提升开发效率。通过合理配置和使用，可以实现：

✅ 快速环境配置  
✅ 灵活的版本管理  
✅ 自动化更新和维护  
✅ 便携化应用部署  
✅ 开发工具链一体化  

**建议：** 结合 Oh-My-Posh、Posh-Git 等增强工具，打造专业的 Windows 开发环境。

---

*最后更新：2025-10-20*

