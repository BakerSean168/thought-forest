---
tags:
  - tech/dev/backend
  - type/concept
  - status/seed
description: Nodejs的包管理器
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[项目实践 MOC]]

---

# Nodejs的包管理器

## 镜像源

```
.npmrc 文件

## 华为镜像源
## registry=https://repo.huaweicloud.com/repository/npm/

## npm 官方在中国大陆地区的镜像源 ？ 淘宝新镜像源
registry=https://registry.npmmirror.com/
disturl=https://registry.npmmirror.com/-/binary/node
ELECTRON_MIRROR=https://registry.npmmirror.com/-/binary/electron/
```

### pnpm切换镜像源

```
pnpm config get registry 
pnpm config get electron_mirror

pnpm config set registry https://registry.npmmirror.com/
```

## 软件包管理器

### npm

npm（Node Packaged Modules）

#### npm 常用命令

| 命令 | 说明 |
| --- | --- |
| `npm init` | 初始化一个新的 npm 项目，并创建一个 `package.json` 文件 |
| `npm install [package]` | 安装指定的 npm 包 |
| `npm install [package] --save` | 安装指定的 npm 包并将其添加到 `dependencies` |
| `npm install [package] --save-dev` | 安装指定的 npm 包并将其添加到 `devDependencies` |
| `npm uninstall [package]` | 卸载指定的 npm 包 |
| `npm update [package]` | 更新指定的 npm 包 |
| `npm list` | 列出当前项目安装的所有 npm 包 |
| `npm list -g` | 列出全局安装的所有 npm 包 |
| `npm outdated` | 列出需要更新的 npm 包 |
| `npm run [script]` | 运行在 `package.json` 中定义的脚本 |
| `npm test` | 运行测试脚本 |
| `npm start` | 运行启动脚本 |
| `npm publish` | 发布 npm 包到 npm 仓库 |
| `npm cache clean --force` | 清理 npm 缓存 |
| `npm config set [key] [value]` | 设置 npm 配置 |
| `npm config get [key]` | 获取 npm 配置 |
| `npm help` | 显示 npm 帮助信息 |
| `npm install -g npm` | 更新npm |

### yarn

#### yarn 常用命令

| 命令 | 说明 |
| --- | --- |
| `yarn init` | 初始化一个新的 yarn 项目，并创建一个 `package.json` 文件 |
| `yarn add [package]` | 安装指定的 yarn 包 |
| `yarn add [package] --dev` | 安装指定的 yarn 包并将其添加到 `devDependencies` |
| `yarn remove [package]` | 卸载指定的 yarn 包 |
| `yarn upgrade [package]` | 更新指定的 yarn 包 |
| `yarn install` | 安装项目中定义的所有依赖 |
| `yarn install --frozen-lockfile` | 使用 `yarn.lock` 文件安装依赖，确保版本一致 |
| `yarn list` | 列出当前项目安装的所有 yarn 包 |
| `yarn global add [package]` | 全局安装指定的 yarn 包 |
| `yarn global remove [package]` | 全局卸载指定的 yarn 包 |
| `yarn global list` | 列出全局安装的所有 yarn 包 |
| `yarn outdated` | 列出需要更新的 yarn 包 |
| `yarn run [script]` | 运行在 `package.json` 中定义的脚本 |
| `yarn test` | 运行测试脚本 |
| `yarn start` | 运行启动脚本 |
| `yarn publish` | 发布 yarn 包到 yarn 仓库 |
| `yarn cache clean` | 清理 yarn 缓存 |
| `yarn config set [key] [value]` | 设置 yarn 配置 |
| `yarn config get [key]` | 获取 yarn 配置 |
| `yarn help` | 显示 yarn 帮助信息 |
| `yarn upgrade-interactive` | 交互式更新依赖包 |

*如果yarn install有误时。可重新install或者清除 重新执行*

#### yarn 版本管理

| 命令 | 说明 |
| --- | --- |
| `npm view yarn versions --json` | 查看yarn历史版本 |
| `npm install yarn@latest -g` | 更新yarn |
| `yarn upgrade v1.21.3` | yarn 升级指定版本 |
| `yarn upgrade v1.21.3` | yarn 降低到指定版本（先卸载，再安装） |

### pnpm

#### pnpm 常用命令

| 命令                                                        | 说明                                      |
| --------------------------------------------------------- | --------------------------------------- |
| `pnpm init`                                               | 初始化一个新的 pnpm 项目，并创建一个 `package.json` 文件 |
| `pnpm install` 或 `pnpm i`                                 | 安装项目中定义的所有依赖                            |
| `pnpm add [package]`                                      | 安装指定的 pnpm 包                            |
| `pnpm add [package] --save-dev` 或 `pnpm add [package] -D` | 安装指定的 pnpm 包并将其添加到 `devDependencies`    |
| `pnpm remove [package]`                                   | 卸载指定的 pnpm 包                            |
| `pnpm update [package]` 或 `pnpm up [package]`             | 更新指定的 pnpm 包                            |
| `pnpm list` 或 `pnpm ls`                                   | 列出当前项目安装的所有 pnpm 包                      |
| `pnpm list -g` 或 `pnpm ls -g`                             | 列出全局安装的所有 pnpm 包                        |
| `pnpm outdated`                                           | 列出需要更新的 pnpm 包                          |
| `pnpm run [script]`                                       | 运行在 `package.json` 中定义的脚本               |
| `pnpm test`                                               | 运行测试脚本                                  |
| `pnpm start`                                              | 运行启动脚本                                  |
| `pnpm publish`                                            | 发布 pnpm 包到 pnpm 仓库                      |
| `pnpm cache clean`                                        | 清理 pnpm 缓存                              |
| `pnpm config set [key] [value]`                           | 设置 pnpm 配置                              |
| `pnpm config get [key]`                                   | 获取 pnpm 配置                              |
| `pnpm help`                                               | 显示 pnpm 帮助信息                            |
| `pnpm install -g [package]`                               | 全局安装指定的 pnpm 包                          |
| `pnpm uninstall -g [package]`                             | 全局卸载指定的 pnpm 包                          |
| `pnpm root -g`                                            | 显示全局安装的 pnpm 包的路径                       |
