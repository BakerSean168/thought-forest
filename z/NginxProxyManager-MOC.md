---
tags:
  - tech/ops
  - status/seed
  - type/moc
  - type/resource
description: NginxProxyManager
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Docker MOC]] | [[DevOps MOC]]

---


# NginxProxyManager MOC

[[Nginx Proxy Management 的基本原理]]

-----

## 3\. 安装与环境准备（Installation / Setup）

  * **系统要求**

      * 一台运行 **Docker** 和 **Docker Compose** 的 Linux/Windows/macOS 主机（推荐 Linux）。
      * 主机上 **80 端口 (HTTP)** 和 **443 端口 (HTTPS)** 未被占用，因为 NPM 需要监听这些端口来处理外部流量并进行证书验证。
      * 需要一个域名指向该主机的公共 IP 地址。

  * **安装步骤（命令行/GUI）**

    1.  **创建 Docker Compose 文件：** 创建一个 `docker-compose.yml` 文件。
    2.  **配置服务：** 至少包含 NPM 容器和数据库容器（SQLite 默认，但推荐使用 MariaDB/MySQL 以获得更高稳定性）。

    <!-- end list -->

    ```yaml
    version: '3.8'
    services:
      app:
        image: 'jc21/nginx-proxy-manager:latest'
        restart: always
        ports:
          - '80:80'    # HTTP 入口
          - '443:443'  # HTTPS 入口
          - '81:81'    # NPM Web 界面
        volumes:
          - ./data:/data
          - ./letsencrypt:/etc/letsencrypt
      db:
        image: 'jc21/mariadb-aria:latest' # 推荐使用 MariaDB
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: 'your_db_root_password' # 替换为安全密码
          MYSQL_DATABASE: 'npm'
          MYSQL_USER: 'npmuser'
          MYSQL_PASSWORD: 'your_npm_password' # 替换为安全密码
        volumes:
          - ./mysql:/var/lib/mysql
    ```

    3.  **启动：** 在 `docker-compose.yml` 所在目录运行 `docker compose up -d`。

  * **环境变量 / 配置说明**

      * `DB_*` 相关的环境变量（如 `DB_MYSQL_HOST` 等）可以用来配置 NPM 连接到外部或单独的数据库容器，如果使用上述默认配置，NPM 会自动连接到 `db` 服务。
      * **默认登录凭证：** 首次启动后，访问 `http://<IP>:81` 登录：
          * **Email:** `admin@example.com`
          * **Password:** `changeme`
          * **警告：** 首次登录后必须立即更改凭证！

-----

## 4\. 快速开始（Quick Start）

  * **基本使用流程**
    1.  **启动 NPM：** 使用 `docker compose up -d`。
    2.  **登录：** 访问 `http://<IP>:81`，并更改默认凭证。
    3.  **部署目标服务：** 确保你要代理的目标服务（如一个后端 App，容器名假设为 `my-backend`，端口 `3000`）已在 Docker 网络中运行。
    4.  **添加代理主机：**
          * 进入 **Proxy Hosts** -\> **Add Proxy Host**。
          * **Domain Names:** `api.mydomain.com`
          * **Forward Hostname/IP:** `my-backend` (容器名)
          * **Forward Port:** `3000`
          * **SSL：** 切换到 SSL 选项卡，选择 **Request a new SSL Certificate** 并勾选 **Force SSL**。
    5.  **Save**。
  * **最小可运行示例**
    ```bash
    # 假设你的目标服务名为 my-app，端口 8080，并且已启动
    # NPM 启动后，通过 Web 界面进行配置，无需命令行操作
    ```

-----

## 5\. 进阶使用（Advanced Usage）

  * **配置项说明**
      * **Websockets 支持：** 对于需要 Websockets 的应用（如 Socket.IO, 某些 Chat App），必须在代理主机的 **Advanced** 选项卡中勾选 **Websockets Support**。
      * **HSTS (HTTP Strict Transport Security)：** 在 SSL 选项卡中勾选，强制客户端浏览器只使用 HTTPS 访问该域名，提高安全性。
  * **可扩展点 / 插件机制**
      * NPM 本身没有官方的插件机制，但它的**核心扩展点**是 **Custom Nginx Configuration**。用户可以通过在 **Advanced** 选项卡中插入 `location` 或 `server` 级别的 Nginx 指令，实现几乎任何自定义功能（如 OAuth 代理、特定的头文件修改等）。
  * **常见模式（patterns）**
    1.  **API Gateway 模式：** 使用 NPM 将不同的子域名 (`api.`, `app.`, `docs.`) 转发到不同的后端服务容器。
    2.  **强制 HTTPS 模式：** 利用 NPM 自动的 Let's Encrypt 功能和 **Force SSL** 选项，确保所有通信都是加密的。

-----

## 6\. 目录结构（Project Structure）

```
/npm-deployment/
  ├── docker-compose.yml   # 部署文件
  ├── data/                # NPM 内部配置和 SQLite 数据库（如果使用内置数据库）
  └── letsencrypt/         # Let's Encrypt 证书和密钥文件
```

  * **每个目录的作用说明**
      * `docker-compose.yml`: 定义 NPM 及其数据库服务的 Docker 配置。
      * `data/`: 存储 NPM 应用本身的配置数据、日志以及内部使用的 SQLite 数据库文件（如果未使用 MariaDB/MySQL）。**必须备份**。
      * `letsencrypt/`: 存储所有通过 Let's Encrypt 获取的 SSL 证书和私钥。**必须备份**。

-----

## 7\. 常见问题（FAQ）

  * **Q1: 为什么我的证书申请失败？**
      * A: 最常见的原因是 **80 端口或 443 端口未正确暴露或被占用**。Let's Encrypt 必须通过 80 端口进行 HTTP-01 验证。确保您的云防火墙/安全组、宿主机防火墙 (`ufw`/`firewalld`) 和其他应用都没有占用这些端口。
  * **Q2: 为什么我代理到前端网站后，二级路由无法访问（404）？**
      * A: 这是单页应用 (SPA) History Mode 的典型问题。NPM 只是反向代理，你的 **前端 Nginx 容器** 必须配置 `try_files $uri $uri/ /index.html;` 来确保所有路由请求都回退到 `index.html`。

-----

## 8\. 排错指南（Debug / Troubleshooting）

  * **常见报错 & 解决方法**
    | 错误信息 | 常见原因 | 解决方法 |
    | :--- | :--- | :--- |
    | **"Internal Error"** (在界面) | 数据库连接失败或 `data/` 目录权限问题。 | 检查 `docker compose logs db` 和 `docker compose logs app`，确保数据库服务正在运行且连接信息正确。|
    | **SSL 申请失败** | 端口 80 无法从外部访问。 | 检查防火墙和云服务安全组设置，确保 80/443 端口完全开放。 |
    | **502 Bad Gateway** | NPM 可以连接到 Nginx，但 Nginx **无法连接到目标服务**。| 确认代理目标的容器名/IP 和端口号是否正确，且它们是否在同一个 Docker 网络中。|
  * **检查顺序**
    1.  **NPM 状态：** 检查 `docker compose ps` 确认 `app` 和 `db` 容器是否都处于 `Up` 状态。
    2.  **网络连接：** 在 NPM 容器内部运行 `ping <target-container-name>` 确认 NPM 是否能访问到目标服务。
    3.  **端口检查：** 确保宿主机的 80/443 端口对外开放。
    4.  **目标服务状态：** 确保目标服务容器自身运行正常，并在内部端口上监听。

-----

## 9\. 最佳实践（Best Practices）

  * **推荐的工作流**
    1.  **使用 MariaDB/MySQL：** 不要依赖默认的 SQLite 数据库，使用独立的 MariaDB/MySQL 容器来存储配置，以提高稳定性和易于备份。
    2.  **专用的 Docker 网络：** 为 NPM 和所有受代理的服务创建一个专用的 Docker 网络，确保它们之间的通信隔离且安全。
    3.  **使用容器名作为转发目标：** 在 NPM 配置中，使用 Docker 容器名（而非 IP）作为转发目标，这样即使容器重启 IP 变化也不会影响代理。
  * **最常见的踩坑**
      * **NPM 与目标服务不在同一网络：** 导致 502 错误。**解决方法：** 确保 `docker-compose.yml` 中的 `networks` 配置正确。
      * **SSL 验证时 80 端口未暴露：** 导致证书申请失败。
  * **性能/安全注意点**
      * **安全性：** 首次登录后立即更改管理员账号和密码。
      * **HSTS：** 开启 HSTS 确保浏览器只使用 HTTPS。
      * **缓存：** 在代理主机的高级配置中，为静态资源添加合适的缓存头，减轻后端压力。

-----

## 10\. 资源与参考（Resources）

  * **官方文档：** [Nginx Proxy Manager GitHub](https://github.com/NginxProxyManager/nginx-proxy-manager)
  * **视频/博客：** 搜索 YouTube 上的 "Nginx Proxy Manager Tutorial" (通常是 Tech-focused 频道)。
  * **Docker Hub 镜像：** [jc21/nginx-proxy-manager](https://hub.docker.com/r/jc21/nginx-proxy-manager)

-----

## 11\. 个人笔记（Personal Notes）

[[NginxPM部署静态资源]]
  * **使用心得：** NPM 极大地简化了 Docker 环境中的 SSL 管理。一旦配置好，后续添加新的服务几乎只需 30 秒。它尤其适合家庭服务器 (Homelab) 和小型项目。
  * **自定义小技巧：** 可以创建一个通配符代理主机 (`*.mydomain.com`)，并使用 **Access Lists** 对一些敏感的内部服务（如数据库面板）设置密码保护，增加一层安全。
  * **后续学习方向：**
      * 学习如何将 **Cloudflare DNS API** 集成到 NPM 中，实现 DNS 模式的证书验证（无需开放 80 端口，适合高级场景）。
      * 深入学习 Nginx 配置文件，以便更好地利用 NPM 的 **Advanced** 配置项进行性能调优。
