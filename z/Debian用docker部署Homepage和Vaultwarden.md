---
tags: [type/howto, status/evergreen, tech/ops/docker]
description: 如何xxx
created: 2026-01-18T21:57:47
updated: 2026-01-18T21:58:31
---

# Debian用docker部署Homepage和Vaultwarden 

### 部署 Homepage 和 Vaultwarden

为了保持服务器整洁，我们将把所有服务都放在 `/opt/docker` 目录下。

**1. 创建目录结构：**

Bash

```
sudo mkdir -p /opt/docker/homepage/config
sudo mkdir -p /opt/docker/vaultwarden/data
cd /opt/docker
```

2. 创建 compose.yaml 文件：

我们需要一个“蓝图”来告诉 Docker 怎么运行这两个软件。

输入命令：

```
nano compose.yaml
```

3. 粘贴以下配置 (核心步骤)：

(我帮你配置好了端口：Homepage 用 3000，Vaultwarden 用 8080)

YAML

```
services:
  # --- 1. Homepage 导航面板 ---
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    ports:
      - 3000:3000
    volumes:
      - ./homepage/config:/app/config # 挂载配置目录
      - /var/run/docker.sock:/var/run/docker.sock:ro # 让它能读取 Docker 状态
    environment:
      PUID: 1000
      PGID: 1000
    restart: unless-stopped

  # --- 2. Vaultwarden 密码管理器 ---
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    ports:
      - 8080:80 # 把容器内的 80 映射到服务器的 8080
    volumes:
      - ./vaultwarden/data:/data
    environment:
      - SIGNUPS_ALLOWED=true  # 首次安装开启注册，注册完建议改成 false
    restart: unless-stopped
```

- 按 `Ctrl + O` 保存，`Enter` 确认，`Ctrl + X` 退出。

**4. 启动服务：**

Bash

```
docker compose up -d
```

Docker 会开始疯狂拉取镜像并启动。等待出现 `Started` 字样即可。

---

### 第三步：访问你的成果

现在，你的 N100 已经不仅仅是一台服务器了：

1. **访问 Homepage**：
	
	- 浏览器打开：`http://192.168.0.51:3000`
	- 你会看到一个默认的漂亮面板。
	- **怎么改内容？** 回到 SSH，编辑 `/opt/docker/homepage/config/services.yaml` 等文件。
		
2. **访问 Vaultwarden**：
	
	- 浏览器打开：`http://192.168.0.51:8080`
	- **⚠️ 重要提示**：Vaultwarden 出于安全考虑，**强制要求 HTTPS** 才能使用部分功能（如创建账号）。
	- **解决方法**：你需要用 **Nginx Proxy Manager** (NPM) 给它套一层反向代理并加上 SSL 证书，或者在你的浏览器里开启“不安全来源”豁免（不推荐）。

建议的下一步：

因为 Vaultwarden 没 HTTPS 用不了，你现在最需要安装 Nginx Proxy Manager 来管理反向代理和证书。

**需要我教你怎么把 NPM 加到这个 `compose.yaml` 里吗？**