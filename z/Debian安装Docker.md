---
tags: [type/howto, status/evergreen, tech/ops/docker]
description: 如何xxx
created: 2026-01-18T21:53:12
updated: 2026-01-18T21:54:16
---

# Debian安装Docker 

### 安装 Docker 环境 

Debian 默认没有 Docker，我们需要安装 Docker 官方版本。请在 SSH 连接中依次复制粘贴以下命令：

**1. 卸载旧版本（防止冲突）：**

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

```

**2. 安装必要工具并添加 Docker 官方 GPG 密钥：**

```bash
# 更新并安装工具
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg

# 添加密钥
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

```

**3. 添加 Docker 软件源：**
*(注意：Debian 13 Trixie 还很新，Docker 官方可能还没有专门的 Trixie 源，我们暂时用 Bookworm (Debian 12) 的源兼容，完全没问题)*

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

**4. 安装 Docker 引擎：**

```bash
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

**5. 启动并测试：**

```bash
sudo systemctl enable docker --now
docker --version
docker compose version

```

*(如果你能看到版本号，说明地基打好了！)*
