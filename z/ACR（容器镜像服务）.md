---
tags: [status/growing, type/concept]
description: 阿里云提供的 Docker 容器镜像服务，让用户可以进行容器的托管。
created: 2025-12-21T22:07:51
updated: 2025-12-22T13:25:42
---

# ACR（容器镜像服务） 

阿里云提供的 Docker 容器镜像服务，让用户可以进行容器的托管。在中国大陆这种多颗网络、官方网路访问受限的地区，使用 ACR 服务是一个更佳的选择。

## 实战

### 第一阶段：控制台配置（只需做一次）

1. **开通个人版：**
* 登录 [阿里云容器镜像服务控制台](https://cr.console.aliyun.com/)。
* 点击 **实例列表** -> **个人版**。点击“创建个人版实例”。
* **设置登录密码：** 这是最重要的一步。在控制台左侧点击 **设置仓库登录密码**。这个密码是你稍后在终端执行 `docker login` 时使用的，**不是**你的阿里云登录密码。

2. **创建命名空间（Namespace）：**
* 点击左侧 **命名空间** -> **创建命名空间**。
* 取一个名字，比如 `baker-dailyuse`。建议设置为“私有”，这样你的代码镜像就不会被别人看到。

3. **创建镜像仓库：**
* 点击左侧 **镜像仓库** -> **创建镜像仓库**。
* 选择刚才创建的命名空间，仓库名称填 `dailyuse-api`。
* 摘要选“私有”，代码源选“本地仓库”。

---

### 第二阶段：本地 Windows 电脑的操作

当你本地运行 `docker build` 成功后，按照以下步骤推送：

1. **登录阿里云仓库：**
在 PowerShell 中输入（将 `cn-hangzhou` 换成你仓库所在的实际区域）：

```powershell
docker login --username=你的阿里云主账号名 registry.cn-hangzhou.aliyuncs.com

```

*此时会提示输入密码，请输入你刚才在控制台设置的“仓库登录密码”。*
2. **给镜像“贴标签”（Tag）：**
你需要把本地的镜像 ID 关联到阿里云的远程地址上。假设你的新镜像 ID 是 `244947d2a295`：

```powershell
docker tag 244947d2a295 registry.cn-hangzhou.aliyuncs.com/baker-dailyuse/dailyuse-api:v1.0.0

```

3. **推送镜像（Push）：**

```powershell
docker push registry.cn-hangzhou.aliyuncs.com/baker-dailyuse/dailyuse-api:v1.0.0

# 构建并推送（使用 -ACR 快捷参数）
.\build-and-push.ps1 -ACR -Tag v1.0.2 -Push
```

*你会发现，第二次推送时速度会极快，因为它只会上传修改过的层。*

---

### 第三阶段：远程 VPS 服务器的操作

回到你的阿里云 VPS（ali-dailyuse），由于它就在阿里云内部，拉取速度会非常惊人：

1. **登录仓库：**

```bash
docker login --username=你的阿里云主账号名 registry.cn-hangzhou.aliyuncs.com

```

2. **拉取镜像（Pull）：**

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/baker-dailyuse/dailyuse-api:v1.0.0

```

3. **启动容器：**

```bash
docker run -d --name dailyuse-api-v1 \
  registry.cn-hangzhou.aliyuncs.com/baker-dailyuse/dailyuse-api:v1.0.0

```

```
# 先登录阿里云 ACR（服务器上）
docker login --username=BakerSean crpi-3po0rmvmxgu205ms.cn-hangzhou.personal.cr.aliyuncs.com

# 拉取并启动
docker compose -f docker-compose.prod.yml --env-file .env pull
docker compose -f docker-compose.prod.yml --env-file .env up -d
```