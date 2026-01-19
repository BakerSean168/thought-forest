---
tags: [status/growing, type/concept]
description: base_template
created: 2025-12-24T11:46:19
updated: 2025-12-24T11:46:53
---

# 类 Linux 的文件架构（FHS, Filesystem Hierarchy Standard） 

在 Windows 上构建类 Linux 的文件架构（FHS, Filesystem Hierarchy Standard）是一种极其高级的整理策略。它的核心逻辑是：**系统归系统（C 盘），数据与环境归自己（D 盘）。**

以下是为您精心设计的 **Windows-Linux 融合式文件架构指南**。

---

## 一、 深度解析：Windows 下的“类 Linux”架构

在 Windows 中，我们通常将 **D 盘** 视为根目录 `/`。

### 1. 核心目录映射表

|**目录名**|**Linux 类比**|**Windows 实际用途**|**整理逻辑**|
|---|---|---|---|
|`D:\bin`|`/usr/local/bin`|**个人脚本与单文件工具**|存放 `ffmpeg.exe`, `yt-dlp.exe` 或自己写的 `.bat`/`.ps1`。**必须加入 PATH**。|
|`D:\etc`|`/etc`|**全局配置与环境变量备份**|存放 Git 配置、SSH 密钥备份、或是软件的 `.ini`/`.yaml` 配置文件。|
|`D:\opt`|`/opt`|**可选软件/大型绿色软件**|存放 WinGet/Scoop 找不到的、解压即用的软件（如某些专业破解软件或免安版 IDE）。|
|`D:\var`|`/var`|**动态变量与大数据块**|存放 Docker 镜像、虚拟机磁盘 (`.vhdx`)、本地数据库文件 (MySQL Data)。|
|`D:\dev`|`/dev`|**设备驱动与固件**|存放电脑的备份驱动、鼠标宏配置文件、打印机安装包等。|
|`D:\home`|`/home`|**用户个人家目录**|包含你的 `Projects` (代码), `Docs` (文档), `Assets` (素材)。|
|`D:\scoop`|`/usr/lib/scoop`|**Scoop 管理中心**|所有的开发环境（Python, Node）和命令行工具都在这里。|
|`D:\tmp`|`/tmp`|**临时交换空间**|存放临时下载解压的东西，定期清理。|

---

## 二、 自动化部署（一键创建结构）

为了方便你重装系统后快速恢复，我为你写好了这个 PowerShell 脚本。你可以将其保存为 `InitFolders.ps1`。

```PowerShell
# 1. 创建核心类 Linux 目录结构
$baseDir = "D:\"
$folders = @("bin", "etc", "opt", "var", "dev", "home", "tmp", "srv")

foreach ($folder in $folders) {
    $path = Join-Path $baseDir $folder
    if (!(Test-Path $path)) {
        New-Item -ItemType Directory -Path $path
        Write-Host "Created: $path" -ForegroundColor Green
    }
}

# 2. 在 home 下创建子目录
$homeSubFolders = @("projects", "docs", "assets", "downloads")
foreach ($sub in $homeSubFolders) {
    $path = Join-Path "D:\home" $sub
    if (!(Test-Path $path)) {
        New-Item -ItemType Directory -Path $path
        Write-Host "Created: $path" -ForegroundColor Cyan
    }
}

# 3. 自动将 D:\bin 加入系统环境变量 Path (需要管理员权限)
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
if ($currentPath -notlike "*D:\bin*") {
    $newPath = $currentPath + ";D:\bin"
    [Environment]::SetEnvironmentVariable("Path", $newPath, "User")
    Write-Host "D:\bin has been added to User PATH." -ForegroundColor Yellow
}
```

---

## 三、 供你复制记录的架构说明书

你可以将以下内容直接复制到你的 **Notion、Obsidian 或 README.md** 中，作为你系统维护的最高准则。

### 📁 Windows 个人系统架构规范 (v1.0)

**原则：**

- **C 盘 (System):** 只放操作系统、WinGet 安装的带有注册表的 GUI 软件。
- **D 盘 (Data/Env):** 所有的开发环境、绿色软件、个人文件。

---

#### 1. 软件管理层 (Software)

- **`/scoop`**: 开发环境总目录。运行 `scoop install` 的所有内容。
- **`/opt`**: 独立软件目录。每个软件一个子文件夹，如 `D:\opt\Navicat`。
- **`/bin`**: 快捷指令集。所有不需要文件夹的单个 `.exe` 全部丢进去。

#### 2. 配置与设备层 (Config & Dev)

- **`/etc`**: 配置文件。建议将 `C:\Users\<Name>\.ssh` 等重要配置备份于此。
- **`/dev`**: 驱动仓库。存放本机的 `DriverBackup`、显卡驱动安装包等。

#### 3. 数据与开发层 (Home)

- **`/home/projects`**: 所有的 Git 仓库和代码项目。
- **`/home/docs`**: 个人文档（同步至 OneDrive）。
- **`/home/assets`**: 图片、视频素材、字体库。

#### 4. 运行数据层 (Variable)

- **`/var/vms`**: WSL2 虚拟硬盘、VMware 虚拟机。
- **`/var/docker`**: Docker Desktop 的数据存储。
- **`/var/cache`**: 某些软件庞大的缓存目录（通过软链接链接到此处）。

---

## 四、 进阶：如何同步这个架构？

既然你想自建 VPS 同步，我的建议是：

1. **同步 `/etc` 和 `/home/docs`**：这是你最重要的资产，必须实时通过 VPS 同步。
2. **不同步 `/var` 和 `/scoop`**：
	
	- `var` 里面的虚拟机镜像太大了，没必要同步。
	- `scoop` 里面的软件可以通过你的一键脚本重新安装，同步二进制文件容易出错。
		
3. **同步 `/bin`**：把你常用的小工具同步到云端，换台电脑直接就能用。

