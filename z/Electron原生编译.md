---
tags:
  - type/howto
  - status/evergreen
description: 如何xxx
---


# Electron 原生模块编译指南 (Windows)

## 问题背景

在 Windows 环境下开发 Electron 应用时，如果使用了原生 Node.js 模块（如 `better-sqlite3`），需要针对 Electron 的运行时重新编译这些模块。否则会遇到类似以下错误：

```
Error: Could not locate the bindings file
```

## 为什么需要原生编译？

### 1. ABI 不兼容

**Node.js 和 Electron 使用不同的 V8 版本和 ABI (Application Binary Interface)**

- **Node.js**: 使用标准 Node.js 运行时（如 Node v22.21.1, ABI v137）
- **Electron**: 内嵌自己的 Node.js 版本（如 Electron v39.2.6 使用不同的 Node ABI）

当你用 `npm install` 或 `pnpm install` 安装原生模块时，它们会针对**系统的 Node.js** 编译。但 Electron 使用的是**内嵌的 Node.js**，ABI 版本不同，导致二进制文件不兼容。

### 2. 原生模块的特性

**better-sqlite3** 是一个原生 C++ 模块：
- 包含 C++ 代码（需要编译）
- 依赖 SQLite C 库
- 生成 `.node` 二进制文件（类似 `.dll`）
- 必须与运行时的 ABI 匹配

### 3. 编译目标差异

| 维度 | npm install | Electron 需要 |
|------|------------|---------------|
| **运行时** | 系统 Node.js | Electron 内嵌 Node |
| **ABI 版本** | 如 v137 | 如 v140 |
| **目标平台** | Node.js API | Electron API |
| **二进制位置** | `build/Release/` | 同样的位置但不同 ABI |

## 解决方案：electron-rebuild

### 工具介绍

**electron-rebuild** 是官方推荐工具，用于重新编译原生模块。

```bash
# 旧版（已废弃）
npm install electron-rebuild

# 新版（推荐）
npm install @electron/rebuild
```

### 工作原理

1. **扫描依赖**：查找 `node_modules` 中所有包含 C++ 绑定的模块
2. **获取 Electron 头文件**：下载对应 Electron 版本的头文件
3. **重新编译**：使用 node-gyp 针对 Electron 的 ABI 重新编译
4. **替换二进制**：将新编译的 `.node` 文件替换原来的

## Windows 编译环境要求

### 必需工具

#### 1. Visual Studio Build Tools 2022

**为什么需要？**
- C++ 编译器（MSVC）
- Windows SDK
- MSBuild 工具链

**安装步骤：**
1. 下载 [Visual Studio Build Tools 2022](https://visualstudio.microsoft.com/downloads/)
2. 选择"使用 C++ 的桌面开发"工作负载
3. 确保包含：
   - MSVC v143 编译器
   - Windows 10/11 SDK
   - CMake 工具

#### 2. Python (可选但推荐)

node-gyp 依赖 Python 2.7 或 3.x

```bash
# 安装 Python 3
winget install Python.Python.3.12
```

### 配置 npm

```bash
# 设置 Visual Studio 版本
npm config set msvs_version 2022

# 验证配置
npm config get msvs_version
```

## 实际操作流程

### 方法 1: 使用 electron-rebuild（推荐）

#### 步骤 1: 安装工具

```bash
# 在项目根目录
pnpm add -D @electron/rebuild
```

#### 步骤 2: 编译所有原生模块

```bash
# 自动扫描并编译所有原生模块
pnpm electron-rebuild

# 或使用旧命令（如果安装的是 electron-rebuild）
pnpm electron-rebuild
```

#### 步骤 3: 编译特定模块

```bash
# 只编译 better-sqlite3
pnpm electron-rebuild -f -w better-sqlite3

# 参数说明：
# -f: force，强制重新编译
# -w: which，指定模块名
```

#### 步骤 4: 指定模块路径（pnpm 专用）

由于 pnpm 使用符号链接和硬链接的存储方式，有时需要指定完整路径：

```bash
pnpm electron-rebuild -f -w better-sqlite3 \
  -m node_modules/.pnpm/better-sqlite3@12.5.0/node_modules/better-sqlite3
```

### 方法 2: package.json 脚本

在 `package.json` 中添加：

```json
{
  "scripts": {
    "rebuild": "electron-rebuild -f -w better-sqlite3",
    "postinstall": "electron-rebuild"
  }
}
```

**优点：**
- 每次 `npm install` 后自动重新编译
- 团队成员共享配置

**缺点：**
- 首次安装时间较长
- CI/CD 环境需要编译工具链

## 验证编译结果

### 检查文件是否生成

```powershell
# Windows PowerShell
Test-Path "node_modules\.pnpm\better-sqlite3@12.5.0\node_modules\better-sqlite3\build\Release\better_sqlite3.node"
```

```bash
# Bash / Git Bash
ls -lh node_modules/.pnpm/better-sqlite3@*/node_modules/better-sqlite3/build/Release/
```

### 预期输出

```
Directory: D:\project\node_modules\.pnpm\better-sqlite3@12.5.0\node_modules\better-sqlite3\build\Release

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/12/30   20:30          850432 better_sqlite3.node
```

## 常见问题与解决

### 问题 1: MSBuild 找不到

```
error MSB8036: The Windows SDK version 10.0 was not found
```

**解决：**
```bash
# 重新安装 Visual Studio Build Tools
# 确保勾选 "Windows 10 SDK"
```

### 问题 2: Python 找不到

```
gyp ERR! stack Error: Can't find Python executable "python"
```

**解决：**
```bash
# 安装 Python
winget install Python.Python.3.12

# 或指定 Python 路径
npm config set python "C:\Python312\python.exe"
```

### 问题 3: node-gyp 错误

```
gyp ERR! build error
gyp ERR! stack Error: `C:\Program Files\MSBuild\...` failed with exit code: 1
```

**解决：**
```bash
# 清理缓存
pnpm store prune
rm -rf node_modules

# 重新安装
pnpm install
pnpm electron-rebuild
```

### 问题 4: pnpm 符号链接问题

```
Error: Cannot find module
```

**解决：**
```bash
# 使用绝对路径
pnpm electron-rebuild -f -w better-sqlite3 \
  -m node_modules/.pnpm/better-sqlite3@12.5.0/node_modules/better-sqlite3
```

### 问题 5: Electron 版本不匹配

```
Error: The module was compiled against a different Node.js version
```

**解决：**
```bash
# 检查 Electron 版本
pnpm list electron

# 指定版本重新编译
pnpm electron-rebuild -v 39.2.6
```

## 在 Nx 项目中集成

### 项目结构

```
dailyuse/
├── apps/
│   └── desktop/              # Electron 应用
│       ├── package.json
│       └── project.json
├── packages/                 # 共享包
└── package.json              # 根配置
```

### 配置 project.json

```json
{
  "targets": {
    "rebuild": {
      "executor": "nx:run-commands",
      "options": {
        "command": "electron-rebuild -f -w better-sqlite3",
        "cwd": "{workspaceRoot}"
      }
    },
    "serve": {
      "executor": "nx:run-commands",
      "dependsOn": ["^build"],
      "options": {
        "command": "vite",
        "cwd": "apps/desktop"
      }
    }
  }
}
```

### nx.json 配置

确保 `serve` 目标会先构建依赖：

```json
{
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"],
      "cache": true
    },
    "serve": {
      "dependsOn": ["^build"],
      "cache": false
    }
  }
}
```

### 运行命令

```bash
# 重新编译原生模块
nx rebuild desktop

# 启动开发服务器（自动构建依赖）
nx serve desktop
```

## 最佳实践

### 1. 开发环境设置

**首次克隆仓库后：**

```bash
# 1. 安装依赖
pnpm install

# 2. 配置 npm（如果需要）
npm config set msvs_version 2022

# 3. 重新编译原生模块
pnpm electron-rebuild

# 4. 启动开发
pnpm nx serve desktop
```

### 2. 团队协作

**添加 .nvmrc**（Node 版本）：
```
v22.21.1
```

**添加 .npmrc**（npm 配置）：
```
msvs_version=2022
```

**README.md 中添加说明：**
```markdown
## 环境要求

- Node.js 22.x
- Visual Studio Build Tools 2022
- Python 3.x (可选)

## 首次启动

\`\`\`bash
pnpm install
pnpm electron-rebuild
pnpm nx serve desktop
\`\`\`
```

### 3. CI/CD 配置

**GitHub Actions 示例：**

```yaml
name: Build Electron App

on: [push]

jobs:
  build:
    runs-on: windows-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '22'
      
      - name: Setup MSBuild
        uses: microsoft/setup-msbuild@v1
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Rebuild native modules
        run: pnpm electron-rebuild
      
      - name: Build app
        run: pnpm nx build desktop
```

### 4. 缓存策略

**本地开发：**
- ✅ 编译后的 `.node` 文件不要删除
- ✅ 只在升级 Electron 版本后重新编译
- ❌ 不要将 `node_modules/.pnpm/*/build/` 加入 `.gitignore`

**CI/CD：**
```yaml
- name: Cache native modules
  uses: actions/cache@v3
  with:
    path: |
      node_modules/.pnpm/better-sqlite3@*/node_modules/better-sqlite3/build
    key: ${{ runner.os }}-electron-${{ hashFiles('pnpm-lock.yaml') }}
```

## 总结

### 核心要点

1. **原生模块必须针对 Electron 的 Node.js ABI 编译**
2. **Windows 需要 Visual Studio Build Tools 2022**
3. **使用 `@electron/rebuild` 或 `electron-rebuild` 工具**
4. **pnpm 用户注意符号链接路径问题**

### 快速检查清单

- [ ] Visual Studio Build Tools 2022 已安装
- [ ] npm config 设置了 `msvs_version=2022`
- [ ] 已运行 `pnpm electron-rebuild`
- [ ] 文件 `better_sqlite3.node` 存在于 `build/Release/` 目录
- [ ] Electron 应用可以正常启动

### 常用命令

```bash
# 检查 Electron 版本
pnpm list electron

# 重新编译所有原生模块
pnpm electron-rebuild

# 强制重新编译 better-sqlite3
pnpm electron-rebuild -f -w better-sqlite3

# 验证编译结果
Test-Path "node_modules\.pnpm\better-sqlite3@12.5.0\node_modules\better-sqlite3\build\Release\better_sqlite3.node"

# 启动 Electron 开发
pnpm nx serve desktop
```

## 参考资源

- [Electron 文档 - 使用原生模块](https://www.electronjs.org/docs/latest/tutorial/using-native-node-modules)
- [@electron/rebuild GitHub](https://github.com/electron/rebuild)
- [node-gyp Windows 编译指南](https://github.com/nodejs/node-gyp#on-windows)
- [better-sqlite3 文档](https://github.com/WiseLibs/better-sqlite3)
- [Visual Studio Build Tools 下载](https://visualstudio.microsoft.com/downloads/)

---

**最后更新**: 2025-12-30  
**适用版本**: Electron 39.x, Node.js 22.x, Windows 10/11
