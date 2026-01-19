---
tags:
  - tech/ops
  - type/howto
  - status/growing
  - type/debug
description: ops
---


# dailyuse的docker镜像构建 

## 前置

[[Docker代理配置]]

配好代理 node 镜像处还是报错，直接先把 单独把镜像拉下来（缓存）

## 命令

### 构建

```PowerShell
# 构建命令 (Build)
docker build -f Dockerfile.api -t bakersean1688/dailyuse-api:v1.0.0 .
```

**参数解释：**
- `-f Dockerfile.api`：告诉 Docker 使用名为 `Dockerfile.api` 的文件作为构建蓝图。
- `-t bakersean1688/dailyuse-api:v1.0.0`：给构建好的镜像起个名字（Tag）。**请确保这里用了你自己的用户名**。
- `.` (最后那个点)：**非常重要！** 这代表“构建上下文”是当前目录。Docker 会把当前目录下所有的文件（包括 `packages/` 和根目录 `package.json`）都发给引擎，这样 Dockerfile 里的 `COPY` 指令才能找到文件。

### 强制构建

```
docker build -f Dockerfile.web --no-cache -t docker.io/dailyuse/dailyuse-web:v1.0.0 .
```

## 问题总结





我直接让AI生成了一个构建的脚本，以及对应的 dockerfile 文件,就没有用到构建命令。 
让 ai 生成了 dockerfile，仍然遇到很多问题

### 第一阶段：网络与环境 (最难啃的骨头)

这一阶段主要解决“Docker 连不上网”的问题。

1.  **超时 (Timeout / Connectex)**
    * **现象**: `dial tcp ... i/o timeout` 或 `connectex: A connection attempt failed`.
    * **原因**: 国内网络环境无法直接访问 Docker Hub 下载基础镜像 (`node:20-alpine`)。
    * **解决**: 配置 Docker Desktop 代理。

2.  **连接被拒绝 (Refused)**
    * **现象**: `connectex: No connection could be made because the target machine actively refused it`.
    * **原因**: 代理软件 (Sparkle) 默认只监听 `127.0.0.1`，而 Docker 容器在虚拟局域网里，被视为“外部设备”，连接被拒绝。
    * **解决**: 开启代理软件的 **"Allow LAN" (允许局域网连接)**。

3.  **连接被重置/无响应 (Firewall Block)**
    * **现象**: 开启 Allow LAN 后依然报错 `connectex` 或无响应。
    * **原因**: **Windows 防火墙** 拦截了 Docker 访问宿主机的代理端口。
    * **解决**: 关闭防火墙（或添加入站规则）。
    * **关键知识点**: Docker 访问宿主机必须用 `host.docker.internal`，不能用 `127.0.0.1`。

4.  **403 Forbidden (镜像源问题)**
    * **现象**: 使用阿里云镜像源时报错 403。
    * **原因**: 国内大厂镜像源策略收紧，不再对公众开放或需要认证。
    * **解决**: **放弃镜像源**，回退到使用代理模式。

---

### 第二阶段：构建与依赖 (pnpm 的坑)

这一阶段主要解决“依赖装不上”或“版本对不上”的问题。

5.  **Lockfile 不匹配**
    * **现象**: `ERR_PNPM_LOCKFILE_CONFIG_MISMATCH`.
    * **原因**: Dockerfile 里用了 `--frozen-lockfile`（要求严格一致），但本地环境和 Docker 环境的 Lock 文件有细微差异。
    * **解决**: 改为 `--no-frozen-lockfile`（允许更新锁文件）。

6.  **缺少开发依赖 (tsup not found)**
    * **现象**: `sh: tsup: not found`.
    * **原因**: 环境变量 `NODE_ENV=production` 导致 pnpm 为了省空间，没安装 `devDependencies`（而打包工具 tsup 恰好在里面）。
    * **解决**: 强制指定 `RUN NODE_ENV=development pnpm install ...`。

---

### 第三阶段：文件结构 (Monorepo 的坑)

这一阶段主要解决“文件漏拷”的问题。Monorepo 项目文件分散，很容易漏。

7.  **根目录文件缺失**
    * **现象**: 即使强制安装开发依赖，还是找不到 `tsup`。
    * **原因**: `Dockerfile` 里漏写了 `COPY package.json ...`。pnpm 不知道根目录下还有依赖要装。
    * **解决**: 把根目录的 `package.json` 加入 COPY 列表。

8.  **生产环境缺少 Lock 文件**
    * **现象**: 生产阶段报错 `ERR_PNPM_NO_LOCKFILE`.
    * **原因**: 多阶段构建（Multi-stage build）中，第二阶段没有从第一阶段复制 `pnpm-lock.yaml`，却用了 `--frozen-lockfile`。
    * **解决**: 生产阶段改用 `--no-frozen-lockfile`。

9.  **工作区包找不到**
    * **现象**: `ERR_PNPM_WORKSPACE_PKG_NOT_FOUND`.
    * **原因**: 生产阶段只复制了 API 代码，没复制 `packages/` 目录下的公共包（如 contracts），导致依赖关系断裂。
    * **解决**: 在生产阶段补充 `COPY packages` 和 `workspace` 配置。

---

### 第四阶段：生命周期脚本 (最后的死循环)

10. **脚本执行失败**
    * **现象**: `Cannot find module ... typescript/bin/tsc`。
    * **原因**: 生产环境只装了生产包 (`--prod`)，没有 `typescript`。但某个子包试图运行 `prepare` 或 `build` 脚本去调用 `tsc`，导致报错。
    * **解决**: 添加 `--ignore-scripts`，告诉 pnpm 只管下载包，别运行包里面的脚本。


