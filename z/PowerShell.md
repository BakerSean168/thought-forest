---
tags:
  - tech/dev/system
  - type/concept
  - status/seed
description: PowerShell
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Resource]] | [[Linux MOC]]

---


# PowerShell 开发者完整指南

## 文档信息

- **创建日期**: 2025-10-18
- **适用场景**: Windows 开发环境，使用 PowerShell 进行开发和项目管理
- **版本**: PowerShell 5.1+（推荐 PowerShell 7.x）
- **目标**: 帮助开发者掌握 PowerShell 核心操作和最佳实践

---

## 📚 目录

1. [基础概念](#基础概念)
2. [使用指南](#使用指南)
3. [信息参考](#信息参考)
4. [常见问题](#常见问题)
5. [开发工具集成](#开发工具集成)

---

## 基础概念

### 什么是 PowerShell

PowerShell 是 Microsoft 开发的任务自动化和配置管理框架，基于 .NET 平台。

#### 版本选择

| 版本    | 名称               | 发布时间 | 推荐指数 | 说明                     |
| ------- | ------------------ | -------- | -------- | ------------------------ |
| **5.1** | Windows PowerShell | 2016     | ⭐⭐⭐      | Windows 内置，广泛兼容   |
| **7.x** | PowerShell (Core)  | 2018+    | ⭐⭐⭐⭐⭐    | 跨平台，现代化，官方推荐 |

#### 运行策略（Execution Policy）

PowerShell 有四个执行策略等级，从最严格到最宽松：

| 策略             | 说明                         | 适用场景           |
| ---------------- | ---------------------------- | ------------------ |
| **Restricted**   | 最严格，不允许运行任何脚本   | 系统默认           |
| **AllSigned**    | 只允许运行已签名的脚本       | 企业环保           |
| **RemoteSigned** | 允许本地脚本，远程脚本需签名 | **推荐用于开发**   |
| **Unrestricted** | 允许所有脚本运行             | 不推荐（安全风险） |

### PowerShell 核心概念

#### 1. 变量和类型

```powershell
# 声明变量（$前缀）
$name = "John"
$age = 25
$active = $true

# 查看变量类型
$age.GetType()  # System.Int32

# 数组
$fruits = @("apple", "banana", "orange")
$fruits[0]  # apple

# 哈希表（字典）
$person = @{
    name = "John"
    age = 25
    city = "Beijing"
}
$person.name  # John
$person["age"]  # 25
```

#### 2. 管道（Pipeline）

PowerShell 的核心特性，将一个命令的输出作为下一个命令的输入：

```powershell
# 基本管道
Get-Process | Where-Object {$_.Name -eq "node"} | Select-Object Name, Id

# 多层管道
Get-ChildItem | Where-Object {$_.Extension -eq ".js"} | ForEach-Object {
    Write-Host $_.Name
}
```

#### 3. 对象和属性

PowerShell 中一切都是对象：

```powershell
# 获取对象属性
Get-Process | Select-Object Name, Id, Handles

# 过滤对象
Get-Process | Where-Object {$_.Handles -gt 1000}

# 统计对象
Get-Process | Measure-Object -Property Handles -Sum
```

---

## 使用指南

### 安装



### I. 执行策略设置（解决脚本禁用问题）

#### ❌ 问题现象

```powershell
pnpm : 无法加载文件 D:\etc\nvm4w\nodejs\pnpm.ps1，
因为在此系统上禁止运行脚本。
```

#### ✅ 解决方案

##### 方案 1: 修改执行策略（推荐）

**Step 1: 以管理员身份打开 PowerShell**

1. 按 `Win + X`
2. 选择 "Windows PowerShell (管理员)" 或 "终端(管理员)"
3. 点击 "是" 确认 UAC 提示

**Step 2: 查看当前执行策略**

```powershell
# 查看当前执行策略
Get-ExecutionPolicy

# 输出示例：
# Restricted  (最严格)
```

**Step 3: 修改执行策略**

```powershell
# 修改为 RemoteSigned（推荐用于开发）
Set-ExecutionPolicy RemoteSigned

# 或者修改为 Unrestricted（不推荐，安全风险较高）
Set-ExecutionPolicy Unrestricted

# 按 'Y' 确认修改
# 输出：执行策略已更改
```

**Step 4: 验证修改**

```powershell
Get-ExecutionPolicy
# 输出：RemoteSigned ✅
```

**Step 5: 重启 PowerShell 使更改生效**

关闭并重新打开 PowerShell，然后测试：

```powershell
pnpm -v
# 输出：9.1.0 ✅（不再出现脚本禁用错误）
```

##### 方案 2: 为单个用户设置（如果无管理员权限）

```powershell
# 查看当前用户的执行策略
Get-ExecutionPolicy -Scope CurrentUser

# 为当前用户修改执行策略（无需管理员权限）
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 验证
Get-ExecutionPolicy -Scope CurrentUser
```

##### 方案 3: 临时绕过（一次性方案）

如果无法修改执行策略，可以在运行脚本时临时绕过：

```powershell
# 方式 1: 使用 -NoProfile 参数
powershell -NoProfile -Command "pnpm -v"

# 方式 2: 使用 -ExecutionPolicy 参数
powershell -ExecutionPolicy Bypass -Command "pnpm -v"
```

---

### II. 常用开发命令

#### 1. 文件和目录操作

```powershell
# 列出目录内容（简写）
ls                          # 列出当前目录
ls -la                      # 列出所有文件（含隐藏文件）和详细信息
Get-ChildItem               # PowerShell 完整命令

# 进入目录
cd D:\myPrograms\DailyUse

# 返回上级目录
cd ..

# 查看当前路径
pwd                         # 简写
Get-Location                # 完整命令

# 创建目录
mkdir src/modules           # 简写
New-Item -ItemType Directory -Path "src/components"

# 创建文件
New-Item -ItemType File -Path "test.txt"
touch test.txt              # 简写（创建空文件）

# 删除文件/目录
rm test.txt                 # 删除文件
rmdir src                   # 删除空目录
Remove-Item -Recurse src    # 递归删除目录及内容

# 查看文件内容
cat package.json            # 简写
Get-Content package.json    # 完整命令

# 搜索文件
Find "error" *.js           # 在所有 .js 文件中搜索 "error"
Select-String -Pattern "error" -Path "*.js"
```

#### 2. 进程管理

```powershell
# 列出所有进程
Get-Process

# 查找特定进程
Get-Process node            # 查找 Node.js 进程
Get-Process | Where-Object {$_.Name -eq "node"}

# 杀死进程
Stop-Process -Name node     # 按名称杀死进程
Stop-Process -Id 1234       # 按 PID 杀死进程

# 查看进程详细信息
Get-Process node | Format-Table Name, Id, Handles, Memory

# 查看内存占用
Get-Process | Sort-Object Memory -Descending | Select-Object Name, Memory -First 10
```

#### 3. 环境变量管理

```powershell
# 查看所有环境变量
$env:Path                   # 查看 PATH 环境变量
$env:NODE_ENV               # 查看 NODE_ENV

# 设置环境变量（临时，仅当前 session）
$env:NODE_ENV = "development"

# 设置环境变量（永久）
[Environment]::SetEnvironmentVariable("NODE_ENV", "production", "User")

# 验证环境变量
$env:NODE_ENV               # development 或 production

# 查看所有系统变量
Get-ChildItem env:
```

#### 4. 权限和执行策略

```powershell
# 查看执行策略
Get-ExecutionPolicy
Get-ExecutionPolicy -List   # 列出所有作用域的执行策略

# 修改执行策略
Set-ExecutionPolicy RemoteSigned

# 以不同权限运行命令
Start-Process PowerShell -Verb RunAs  # 以管理员身份运行 PowerShell
```

---

### III. Node.js/pnpm 开发命令

#### 1. Node.js 版本管理（NVM）

```powershell
# 查看已安装版本
nvm list

# 安装 Node.js
nvm install 20.11.0         # 安装指定版本
nvm install lts             # 安装最新 LTS

# 切换版本
nvm use 20.11.0
nvm use lts

# 设置默认版本
nvm alias default 20.11.0

# 卸载版本
nvm uninstall 18.0.0

# 查看可用版本
nvm list available          # 列出所有可用版本
```

#### 2. Corepack 管理

```powershell
# 启用 Corepack
corepack enable

# 禁用 Corepack
corepack disable

# 查看 Corepack 缓存
corepack cache list

# 清除缓存
corepack cache clean

# 预准备包管理器
corepack prepare pnpm@9.1.0 --activate

# 使用指定版本
corepack use pnpm@9.1.0
```

#### 3. pnpm 项目管理

```powershell
# 安装依赖
pnpm install
pnpm i                      # 简写

# 添加依赖
pnpm add lodash             # 生产依赖
pnpm add -D vitest          # 开发依赖
pnpm add -O typescript      # 可选依赖

# 删除依赖
pnpm remove lodash
pnpm rm lodash              # 简写

# 更新依赖
pnpm update                 # 更新所有依赖
pnpm update lodash          # 更新指定依赖

# 运行脚本
pnpm dev                    # 运行 package.json 中的 dev 脚本
pnpm build
pnpm test
pnpm run <script-name>      # 运行自定义脚本

# Monorepo 操作
pnpm -r install             # 在所有 workspace 中安装依赖
pnpm -r build               # 在所有 workspace 中构建
pnpm -C apps/api install    # 只在指定 workspace 中操作

# 查看依赖树
pnpm ls                     # 查看所有依赖
pnpm ls lodash              # 查看特定依赖
pnpm ls --depth 3           # 查看 3 层深度的依赖树
```

#### 4. Git 操作

```powershell
# 查看状态
git status

# 添加文件
git add .                   # 添加所有改动
git add src/                # 添加指定目录

# 提交
git commit -m "feat: add new feature"

# 查看日志
git log
git log --oneline           # 简洁格式

# 分支管理
git branch                  # 查看本地分支
git branch -a               # 查看所有分支
git branch feature/auth     # 创建新分支
git checkout feature/auth   # 切换分支
git checkout -b feature/auth  # 创建并切换分支

# 推送和拉取
git push
git pull
git fetch

# 合并分支
git merge feature/auth

# 查看差异
git diff
git diff feature/auth
```

---

### IV. 脚本编写基础

#### 1. 创建 PowerShell 脚本

```powershell
# 创建脚本文件
New-Item -ItemType File -Path "setup.ps1"

# 脚本内容示例
# ============ setup.ps1 ============
Write-Host "Setup started..." -ForegroundColor Green

# 检查 Node.js 版本
$nodeVersion = node -v
Write-Host "Node.js version: $nodeVersion"

# 检查 pnpm 版本
$pnpmVersion = pnpm -v
Write-Host "pnpm version: $pnpmVersion"

# 安装依赖
Write-Host "Installing dependencies..." -ForegroundColor Yellow
pnpm install

Write-Host "Setup completed!" -ForegroundColor Green
```

#### 2. 运行脚本

```powershell
# 方式 1: 直接运行（需要修改执行策略）
.\setup.ps1

# 方式 2: 使用 -ExecutionPolicy 参数
powershell -ExecutionPolicy Bypass -File setup.ps1

# 方式 3: 通过 NPM 脚本运行
# package.json 中添加：
# "scripts": {
#   "setup": "powershell -ExecutionPolicy Bypass -File scripts/setup.ps1"
# }
# 然后运行：pnpm setup
```

#### 3. 常用脚本函数

```powershell
# 颜色输出
Write-Host "Success!" -ForegroundColor Green
Write-Host "Warning!" -ForegroundColor Yellow
Write-Host "Error!" -ForegroundColor Red

# 提示用户输入
$response = Read-Host "Continue? (Y/N)"
if ($response -eq "Y") {
    Write-Host "Continuing..."
}

# 检查文件/目录是否存在
if (Test-Path "node_modules") {
    Write-Host "node_modules exists"
}

# 条件判断
if ($LASTEXITCODE -eq 0) {
    Write-Host "Command succeeded"
} else {
    Write-Host "Command failed" -ForegroundColor Red
    exit 1
}

# 循环
@("setup.ts", "config.ts", "main.ts") | ForEach-Object {
    Write-Host "Processing: $_"
}

# 错误处理
try {
    pnpm install
} catch {
    Write-Host "Error: $_" -ForegroundColor Red
    exit 1
}
```

---

## 信息参考

### PowerShell 内置别名

| 别名    | 完整命令                       | 说明          |
| ------- | ------------------------------ | ------------- |
| `pwd`   | `Get-Location`                 | 打印工作目录  |
| `cd`    | `Set-Location`                 | 改变目录      |
| `ls`    | `Get-ChildItem`                | 列出目录内容  |
| `cat`   | `Get-Content`                  | 显示文件内容  |
| `rm`    | `Remove-Item`                  | 删除文件/目录 |
| `mkdir` | `New-Item -ItemType Directory` | 创建目录      |
| `touch` | `New-Item -ItemType File`      | 创建文件      |
| `echo`  | `Write-Output`                 | 输出文本      |
| `which` | `Get-Command`                  | 查找命令位置  |
| `type`  | `Get-Content`                  | 显示文件内容  |

### 环境变量常见值

| 变量               | 说明               | 示例                                  |
| ------------------ | ------------------ | ------------------------------------- |
| `$env:USERNAME`    | 当前用户名         | `JohnDoe`                             |
| `$env:USERPROFILE` | 用户主目录         | `C:\Users\JohnDoe`                    |
| `$env:TEMP`        | 临时目录           | `C:\Users\JohnDoe\AppData\Local\Temp` |
| `$env:Path`        | 可执行文件搜索路径 | `C:\Program Files\Node.js;...`        |
| `$env:NODE_ENV`    | Node.js 环境       | `development`, `production`           |
| `$env:PWD`         | 当前工作目录       | `D:\myPrograms\DailyUse`              |

### 执行策略作用域

| 作用域            | 说明       | 影响范围                   |
| ----------------- | ---------- | -------------------------- |
| **Process**       | 仅当前进程 | 临时生效                   |
| **CurrentUser**   | 当前用户   | 所有 PowerShell 会话       |
| **LocalMachine**  | 所有用户   | 整个系统（需要管理员权限） |
| **MachinePolicy** | 系统策略   | 由管理员设置，优先级最高   |

---

## 常见问题

### Q1: 为什么要修改执行策略？

**A**: PowerShell 默认的 `Restricted` 策略禁止运行任何脚本，这会导致 pnpm、yarn 等工具无法正常工作。修改为 `RemoteSigned` 是开发环境的推荐做法。

### Q2: `RemoteSigned` 安全吗？

**A**: 是的。`RemoteSigned` 允许本地脚本运行，但远程脚本需要签名。这对开发环境足够安全。

### Q3: 如何检查我的 PowerShell 版本？

```powershell
$PSVersionTable.PSVersion
# 或
powershell -v
```

### Q4: 能否为不同项目使用不同的执行策略？

**A**: 可以。可以在脚本运行时指定：

```powershell
powershell -ExecutionPolicy RemoteSigned -File script.ps1
```

### Q5: 如何在 PowerShell 中使用 Bash 命令？

**A**: 如果安装了 Git Bash 或 WSL，可以使用：

```powershell
# 使用 Git Bash
bash -c "ls -la"

# 使用 WSL
wsl ls -la
```

或者切换到 PowerShell 7（推荐），它的兼容性更好。

---

## 开发工具集成

### VSCode PowerShell 集成

#### 1. 安装 PowerShell 扩展

在 VSCode 中搜索并安装 "PowerShell" 扩展（Microsoft 官方）。

#### 2. 配置默认终端

**文件 -> 偏好设置 -> 设置**，搜索 "terminal.integrated.shell.windows"：

```json
{
  "terminal.integrated.shell.windows": "C:\\Program Files\\PowerShell\\7\\pwsh.exe",
  "terminal.integrated.shellArgs.windows": []
}
```

#### 3. 配置 .vscode/settings.json

```json
{
  "powershell.codeFormatting.enabled": true,
  "powershell.linting.enabled": true,
  "[powershell]": {
    "editor.defaultFormatter": "ms-vscode.powershell",
    "editor.formatOnSave": true,
    "editor.tabSize": 4
  }
}
```

### 创建开发快速启动脚本

创建 `scripts/dev-setup.ps1`：

```powershell
# 开发环境快速启动脚本
param(
    [switch]$Install,
    [switch]$Clean
)

Write-Host "=== Development Setup ===" -ForegroundColor Cyan

# 清理依赖
if ($Clean) {
    Write-Host "Cleaning dependencies..." -ForegroundColor Yellow
    Remove-Item -Recurse -Force node_modules -ErrorAction SilentlyContinue
    Remove-Item pnpm-lock.yaml -ErrorAction SilentlyContinue
}

# 检查 Node.js
Write-Host "Checking Node.js..." -ForegroundColor Green
$nodeVersion = node -v
Write-Host "Node.js: $nodeVersion"

# 检查 pnpm
Write-Host "Checking pnpm..." -ForegroundColor Green
$pnpmVersion = pnpm -v
Write-Host "pnpm: $pnpmVersion"

# 启用 Corepack
Write-Host "Enabling Corepack..." -ForegroundColor Green
corepack enable

# 安装依赖
if ($Install -or $Clean) {
    Write-Host "Installing dependencies..." -ForegroundColor Green
    pnpm install
}

# 验证设置
Write-Host "Verification:" -ForegroundColor Green
Write-Host "✓ Node.js: $(node -v)"
Write-Host "✓ pnpm: $(pnpm -v)"
Write-Host "✓ npm: $(npm -v)"

Write-Host "Setup completed successfully!" -ForegroundColor Green
```

使用方式：

```powershell
# 仅检查环境
.\scripts\dev-setup.ps1

# 安装依赖
.\scripts\dev-setup.ps1 -Install

# 清理并重新安装
.\scripts\dev-setup.ps1 -Clean -Install
```

---

## 总结

### ✅ 核心要点

1. **执行策略**: 修改为 `RemoteSigned` 以支持脚本运行
2. **PowerShell 版本**: 推荐使用 PowerShell 7.x 获得更好的跨平台支持
3. **管道操作**: 利用管道连接命令，这是 PowerShell 的核心特性
4. **环境变量**: 了解和设置环境变量对开发至关重要

### 📋 快速检查清单

完成开发环境设置前，确保：

- [ ] PowerShell 版本 >= 5.1（推荐 7.x）
- [ ] 执行策略已修改为 `RemoteSigned`
- [ ] Node.js 通过 NVM 安装
- [ ] Corepack 已启用
- [ ] pnpm 可正常运行
- [ ] 项目中配置了 `packageManager` 字段

### 🚀 下一步

1. 参考"使用指南"部分熟悉常用命令
2. 根据需要创建自动化脚本
3. 参考"开发工具集成"配置 IDE
4. 遇到问题时查看"常见问题"部分

---

**文档版本**: v1.0  
**最后更新**: 2025-10-18  
**维护者**: 开发团队


