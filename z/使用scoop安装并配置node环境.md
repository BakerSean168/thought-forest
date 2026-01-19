---
tags: [type/howto, status/evergreen]
description: 如何xxx
created: 2025-12-24T18:52:59
updated: 2025-12-24T19:44:17
---

# 使用 scoop 安装并配置 node 环境 

## 安装 nvm

scoop 能够 直接安装 node，但最佳实践是先装 nvm，然后通过 nvm 管理 node。

|**特性**|**通过 nvm-windows 管理**|**通过 Scoop 直接安装**|
|---|---|---|
|**多版本共存**|**极佳**。可以同时安装 v14, v16, v18, v20，随时切换。|**较差**。虽然可以使用 `scoop reset` 切换，但过程不够直接。|
|**切换便捷度**|输入 `nvm use 18.1.0` 瞬间完成。|需要运行 `scoop reset nodejs-lts` 等命令。|
|**国内加速**|**支持**。可以方便地配置淘宝/腾讯镜像，下载极快。|依赖 Scoop 的代理配置，有时下载大包依然不稳定。|
|**项目兼容性**|完美。不同项目要求不同 Node 版本时，nvm 是行业标准。|适合只用最新 LTS 版本，不折腾旧项目的人。|

直接在 UniGetUI 中安装 nvm 点击安装就行。

## 配置 npm 镜像

安装完 nvm 后，先不要急着装 Node。因为你在中国大陆，**务必**执行以下两条命令，否则下载 Node 会非常慢：

```powershell
nvm node_mirror https://npmmirror.com/mirrors/node/
nvm npm_mirror https://npmmirror.com/mirrors/npm/
```

## 通过 nvm 安装 Node

想安装 24.12.0，结果镜像站里面还没有，只好先配置代理到官方下载了。相关命令如下：

```powershell

# 把镜像改回官方
nvm node_mirror https://nodejs.org/dist/

nvm proxy http://127.0.0.1:7890

# 查看当前生效代理
nvm proxy

# 清除代理
nvm proxy none
```

- **查看可用版本：** `nvm list available`
- **安装 LTS 版：** `nvm install 20.10.0` (以当前 LTS 为例)
- **使用该版本：** `nvm use 20.10.0`
- **验证：** `node -v`

## 配置 包管理器 corepack

[corepack](https://nodejs.org/api/corepack.html)

在 `corepack` 出现之前，我们通常需要运行 `npm install -g pnpm`。但使用 `corepack` 有以下优势：

- **无需手动安装：** 你不需要通过 `npm` 去全局下载 `pnpm`，它直接由 Node.js 官方提供的工具管理。
- **版本锁定：** 如果你的项目在 `package.json` 中指定了 `packageManager` 字段，`corepack` 会自动帮你切换到项目要求的 `pnpm` 版本。
- **环境整洁：** 配合你目前的 `nvm` 环境，`pnpm` 会随着你切换 Node 版本而自动适配。

以使用 pnpm 为例：

```powershell
# 启用 corepack
corepack enable

# 下载并激活最新版本
corepack prepare pnpm@latest --activate

pnpm -v
```

使用 `nvm` 来管理多个 Node.js 版本，需要了解以下逻辑：

- **版本隔离：** `corepack` 的状态（是否启用）是针对**当前活跃的 Node.js 版本**的。
- **切换版本后：** 如果你以后使用 `nvm use` 切换到了另一个大版本的 Node（比如从 v22 切换到 v20），你可能需要**再次运行一次** `corepack enable` 来在该版本下激活 `pnpm`。

## 命令速查表

### nvm

*windows下的node.js版本管理器*

| 命令                         | 说明                                                                             | 例子  |
| -------------------------- | ------------------------------------------------------------------------------ | --- |
| `nvm list`                 | 查看已经安装的版本                                                                      |     |
| `nvm list installed`       | 查看已经安装的版本                                                                      |     |
| `nvm list available`       | 查看网络可以安装的版本                                                                    |     |
| `nvm arch`                 | 查看当前系统的位数和当前nodejs的位数                                                          |     |
| `nvm install [arch]`       | 安装制定版本的node 并且可以指定平台 version 版本号 arch 平台                                       |     |
| `nvm on`                   | 打开nodejs版本控制                                                                   |     |
| `nvm off`                  | 关闭nodejs版本控制                                                                   |     |
| `nvm proxy [url]`          | 查看和设置代理                                                                        |     |
| `nvm node_mirror [url]`    | 设置或者查看setting.txt中的node_mirror，如果不设置的默认是 https://nodejs.org/dist/              |     |
| `nvm npm_mirror [url]`     | 设置或者查看setting.txt中的npm_mirror,如果不设置的话默认的是：https://github.com/npm/npm/archive/. |     |
| `nvm uninstall`            | 卸载指定的版本                                                                        |     |
| `nvm use [version] [arch]` | 切换指定的node版本和位数                                                                 |     |
| `nvm root [path]`          | 设置和查看root路径                                                                    |     |
| `nvm version`              | 查看当前的版本                                                                        |     |

### corepack 常用命令

| 命令                                                | 说明                          | 例子                                      |
| ------------------------------------------------- | --------------------------- | --------------------------------------- |
| `corepack enable`                                 | 启用 Corepack 以管理包管理器         |                                         |
| `corepack disable`                                | 禁用 Corepack                 |                                         |
| `corepack prepare [package]@[version] --activate` | 准备并激活指定版本的包管理器（如 Yarn、pnpm） | corepack prepare pnpm@latest --activate |
| `corepack prepare [package]@[version]`            | 准备指定版本的包管理器，但不激活            |                                         |
| `corepack use [package]@[version]`                | 使用指定版本的包管理器                 |                                         |
| `corepack shim`                                   | 安装或更新 Corepack 的 shim 脚本    |                                         |
| `corepack unshim`                                 | 移除 Corepack 的 shim 脚本       |                                         |
| `corepack install`                                | 安装项目中定义的所有依赖                |                                         |
| `corepack run [package]@[version] [command]`      | 使用指定版本的包管理器运行命令             |                                         |
| `corepack list`                                   | 列出所有已安装的包管理器及其版本            |                                         |
| `corepack --version`                              | 显示 Corepack 的版本             |                                         |
| `corepack help`                                   | 显示 Corepack 的帮助信息           |                                         |


[[Nodejs的包管理器]]