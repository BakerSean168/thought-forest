---
tags:
  - status/growing
  - tech/ops/deploy
  - type/howto
description: base_template
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[知识库管理体系 MOC]]

---


# DailyUse的api端部署 


## docker 部署（基于镜像仓库部署）

核心思想是：**在本地或 CI/CD 流水线中把镜像构建好，推送到仓库，服务器只负责拉取和运行。**

**服务器上只需要放以下文件：**
1. `docker-compose.prod.yml` (或者 `docker-compose.yml`)
2. `.env` (环境变量文件)
3. `nginx.conf` (因为它是挂载进容器的配置)
4. 其他挂载的证书或配置文件
    

**流程：**
1. **本地/CI环境**：运行 `docker build` 构建镜像 - [[dailyuse的docker镜像构建]]。
2. **推送**：将镜像推送到 Docker Hub、阿里云容器镜像服务或 GitHub Container Registry - [[dailyuse的Docker镜像推送]]。
3. **服务器**：运行 `docker-compose pull` 拉取镜像，然后 `up -d` 启动 - [[dailyuse的Docker镜像的拉取]]。
    

**优点：**
- **安全**：服务器上没有源代码，避免代码泄露。
- **快速**：服务器不需要消耗 CPU/内存去进行 `npm install` 或编译，启动极快。
- **环境一致**：保证了测试环境和生产环境运行的是完全同一个二进制镜像。

优化

帮我看看是不是确实存在让 镜像部署过程中去下载的问题。还有现在我已经使用 [[ACR（容器镜像服务）]] 来解决docker 网络问题了。直接让脚本推送，我已经登录了
```
crpi-3po0rmvmxgu205ms.cn-hangzhou.personal.cr.aliyuncs.com
命名空间 bakersean
可以在仓库不存在的情况下直接推送镜像，系统会自动创建对应的仓库。
```