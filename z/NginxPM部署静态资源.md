---
tags:
  - tech/ops
  - type/concept
  - status/seed
description: NginxPM部署静态资源
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---


# NginxPM部署静态资源 

> [!NOTE] 注意
> NPM 配置的静态文件一般只能通过域名访问，不能用ip，他监听不到。

应用配置并重启

```bash

docker compose down
docker compose up -d
# 或
docker compose restart （npm）

```

## 方案二：通过 Volume 挂载部署静态文件

两个核心配置步骤：

1.  **修改 Docker Compose 文件：** 定义卷挂载，让 NPM 容器能看到您的项目目录。
2.  **配置 NPM 代理主机：** 使用高级配置，让容器内部的 Nginx 知道去哪里读取文件。

假设您的静态项目路径仍为：`/opt/web/resume`。

### 步骤一：修改 Docker Compose 文件 (`docker-compose.yml`)

您需要编辑您用来启动 NPM 的 `docker-compose.yml` 文件。

在 `volumes` 部分添加一行新的挂载配置：

```yaml
version: '3.8'
services:
  npm:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginx-proxy-manager
    restart: always
    ports:
      - '80:80'
      - '443:443'
      - '81:81'
    environment:
      # ... 其他环境变量
      TZ: Asia/Shanghai # 示例时区
    volumes:
      # 1. NPM 数据持久化目录 (宿主机:容器内部)
      - /path/to/npm/data:/data 
      # 2. SSL 证书持久化目录 (宿主机:容器内部)
      - /path/to/npm/letsencrypt:/etc/letsencrypt
      
      # === 关键：挂载您的静态文件目录 ===
      # 将宿主机上的 /opt/web/resume 映射到容器内部的 /app/static-resume
      - /opt/web/resume:/app/static-resume:ro 
```

#### ✍️ 卷挂载说明：

  * **宿主机路径：** `/opt/web/resume` (您的项目路径)
  * **容器内部路径：** `/app/static-resume` (这是一个您自定义的、将在容器内部使用的路径)
  * **`:ro`：** 表示只读（Read Only），这是推荐做法，防止容器内的进程意外修改您的项目文件。

#### 2\. 应用配置并重启 NPM

在您的 `docker-compose.yml` 文件所在的目录执行：

```bash
docker compose down
docker compose up -d
```

或

```bash
docker compose restart npm
```

-----

### 步骤二：在 Nginx Proxy Manager (NPM) 中设置高级配置

现在，我们需要告诉 NPM 自带的 Nginx 服务：当收到特定域名的请求时，不要代理，而是直接从我们挂载的 `/app/static-resume` 路径提供文件。

#### 1\. 添加代理主机 (Proxy Host)

点击 **"Proxy Hosts"**，然后点击 **"Add Proxy Host"**。

| 字段 | 设置值 | 说明 |
| :--- | :--- | :--- |
| **Domain Names** | `resume.yourdomain.com` (您的域名) | |
| **Scheme/Forward Hostname/Port** | 随意填写（例如：`http://127.0.0.1:81`） | **注意：** 这里的设置会被高级配置覆盖，但 NPM 要求填写。 |

#### 2\. 配置 Advanced 标签页

切换到 **"Advanced"** 标签页，在 **"Custom Nginx Configuration"** 框中输入以下配置：

```nginx
# === 覆盖默认的代理配置 ===
location / {
    # alias 指令用于指定文件系统的路径
    # 它必须指向您在 Docker Compose 中挂载的容器内部路径
    alias /app/static-resume/; 
    
    # 设定默认索引文件
    index index.html index.htm;
    
    # 可选：如果你在做单页应用(SPA)，并且需要路由回退到 index.html
    # try_files $uri $uri/ /index.html; 
}

# 可选：为静态资源设置缓存，加快后续访问速度
location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff2?|ttf|eot|svg)$ {
    # 匹配静态文件，并设置缓存
    alias /app/static-resume/;
    expires 30d;
    add_header Cache-Control "public, max-age=2592000";
    # 阻止 try_files 逻辑
    try_files $uri =404; 
}
```

#### 3\. 配置 SSL (强烈推荐)

切换到 **"SSL"** 标签页，像往常一样配置您的 Let's Encrypt 证书并勾选 **"Force SSL"**。

#### 4\. 保存并测试

点击 **"Save"**。NPM 将生成并应用包含您的自定义 `location` 块的 Nginx 配置。现在，当您访问 `https://resume.yourdomain.com` 时，NPM 将直接从容器内部挂载的 `/app/static-resume` 路径提供您的简历文件。

-----

### 缺点：

  * **配置复杂：** 需要同时修改 Docker Compose 和 NPM 配置，容易出错。
  * **耦合性高：** 您的前端文件路径被硬编码进了 NPM 的 Docker 配置和 Nginx 配置中，维护起来不如方案一简单。

