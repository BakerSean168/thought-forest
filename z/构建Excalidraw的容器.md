---
tags:
  - tech/ops/docker
  - type/howto
  - status/growing
description: Docker Compose部署Excalidraw白板应用
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Docker MOC]] | [[VPS 资源与综合应用 MOC]]

---


# 构建Excalidraw的容器 

要通过 `docker-compose` 构建部署 Excalidraw，需结合其代码库中 `docker-compose.yml` 及相关配置文件的设计逻辑。以下是详细步骤和配置解析：

## 实战经验
### 一、准备工作

1. **克隆代码库**  
   首先获取 Excalidraw 源码：

   ```bash
   git clone https://github.com/excalidraw/excalidraw.git
   cd excalidraw
   ```

2. **依赖要求**  
   确保本地安装了 `docker` 和 `docker-compose`（或兼容的 `docker compose` 插件）。

### 二、核心配置文件解析（`docker-compose.yml`）

Excalidraw 的 `docker-compose.yml` 定义了开发环境的容器配置，内容如下（关键部分已标注说明）：

```yaml
version: "3.8"  # docker-compose 语法版本

services:
  excalidraw:  # 服务名称
    build:  # 构建镜像的配置
      context: .  # 构建上下文：当前目录（即代码库根目录）
      args:  # 传递给 Dockerfile 的构建参数
        - NODE_ENV=development  # 指定开发环境，用于构建过程（如安装依赖、编译逻辑）
    container_name: excalidraw  # 容器名称
    ports:
      - "3000:80"  # 端口映射：本地 3000 端口 → 容器内 80 端口（容器内服务监听 80）
    restart: on-failure  # 容器故障时自动重启
    stdin_open: true  # 保持标准输入打开（类似 `docker run -i`，方便调试）
    healthcheck:
      disable: true  # 禁用健康检查（开发环境简化配置）
    environment:
      - NODE_ENV=development  # 运行时环境变量（影响应用运行逻辑）
    volumes:  # 数据卷挂载（开发环境关键配置）
      - ./:/opt/node_app/app:delegated  # 本地代码根目录 → 容器内应用目录（实时同步代码，支持热重载）
      - ./package.json:/opt/node_app/package.json  # 单独挂载依赖配置文件（避免覆盖）
      - ./yarn.lock:/opt/node_app/yarn.lock  # 单独挂载依赖锁文件
      - notused:/opt/node_app/app/node_modules  # 用空卷覆盖容器内 node_modules（避免本地无依赖时容器内依赖被删除）

volumes:
  notused:  # 定义空卷，用于隔离 node_modules
```

### 三、构建与部署步骤

#### 1. 构建镜像

通过 `docker-compose build` 命令根据配置构建镜像。构建过程依赖代码库根目录下的 `Dockerfile`（未直接提供，但可通过关联配置推断其逻辑）：

```bash
cd /home/llh-program/excalidraw && docker build -t excalidraw .
```

**构建逻辑推测**（基于 `package.json` 和 `docker-compose` 关联配置）：  
- `Dockerfile` 会基于 Node 镜像（如 `node:18`，参考 `excalidraw-app/package.json` 中 `engines.node` 要求）。  
- 接收 `NODE_ENV=development` 构建参数，执行依赖安装（`yarn install`）。  
- 运行构建命令（如 `yarn build:app:docker`，来自 `excalidraw-app/package.json`，实际执行 `vite build` 生成静态资源）。  
- 最终使用 Nginx 等 Web 服务器托管静态资源（因端口映射到 80，通常是 Nginx 监听 80 端口）。

#### 2. 启动容器

构建完成后，通过 `docker` 启动服务：

```bash

docker run -d --name excalidraw -p 3000:80 excalidraw-simplify


```

#### 3. 访问应用

容器启动后，通过本地 `3000` 端口访问 Excalidraw：  
打开浏览器访问 `http://localhost:3000`，即可看到 Excalidraw 应用界面。

### 四、关键配置说明

1. **构建上下文与参数**  
   - `context: .` 表示以当前目录为构建上下文，`Dockerfile` 需放在根目录，否则需通过 `dockerfile` 字段指定路径。  
   - `args: NODE_ENV=development` 用于告知构建过程（如 `vite` 打包）使用开发环境配置（可能包含源码映射、未压缩资源等）。

2. **端口映射**  
   `3000:80` 表示容器内 Web 服务器（如 Nginx）监听 80 端口，本地通过 3000 端口访问。

3. **数据卷挂载**  
   - 开发环境核心是**实时同步代码**：`./:/opt/node_app/app` 让本地代码修改直接同步到容器内，配合开发服务器（如 `vite dev`）实现热重载。  
   - `node_modules` 用空卷 `notused` 隔离：避免本地未安装依赖时，容器内 `node_modules` 被本地空目录覆盖，确保依赖正常加载。

4. **环境变量**  
   `NODE_ENV=development` 影响应用运行时行为（如日志级别、功能开关），与构建参数区分（构建参数影响打包，环境变量影响运行）。

### 五、生产环境构建对比（参考 GitHub Actions 配置）

`docker-compose.yml` 是开发环境配置，生产环境构建可参考代码库的 `publish-docker.yml`（GitHub Actions 工作流）：  
- 触发时机：`release` 分支推送时。  
- 构建逻辑：使用 `docker/build-push-action` 构建多平台镜像（`linux/amd64`, `linux/arm64`, `linux/arm/v7`），并推送到 DockerHub（标签 `excalidraw/excalidraw:latest`）。  
- 生产环境更注重镜像体积优化、多平台支持和自动化部署，而非开发热重载。

### 六、常见问题

1. **依赖安装失败**：确保 `package.json` 和 `yarn.lock` 完整，可尝试删除本地 `node_modules` 后重新构建。  
2. **端口冲突**：若本地 3000 端口被占用，修改 `ports` 字段（如 `3001:80`）。  
3. **代码同步延迟**：`delegated` 模式是本地到容器的异步同步，若需实时性可改为 `cached`（但性能可能下降）。

通过以上步骤，即可基于 `docker-compose` 构建并部署 Excalidraw 开发环境，体验实时代码修改与应用预览。

