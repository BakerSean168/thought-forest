---
tags: [tech/ops, type/debug, status/growing, type/howto]
description: 本文主要记录了在 Daily Use 项目的 Docker 部署时，Docker 镜像的一些错误的排查。
tool: docker
created: 2025-12-12T21:31:47
updated: 2025-12-12T22:24:58
---

# dailyuse镜像错误排查

## 命令

### 查看日志

```bash
docker logs dailyuse-api

docker logs dailyuse-web
```


### 清理旧镜像 (Prune) - 可选

因为你用新镜像覆盖了旧的 `v1.0.0`，旧的那个镜像会变成“悬空镜像”（名字是 `<none>`），占用磁盘空间。可以清理一下：

```Bash
docker image prune -f
```

### 强制重新创建容器

```bash
# 强制重新创建容器，这样它就会使用刚刚 load 进去的新镜像 
docker compose up -d --force-recreate
```

## 能够访问但是404

```
GET http://4.2.3.28:8080/ 404 (Not Found)
[新] 使用 Edge 中的 Copilot 来解释控制台错误: 单击
         
         以说明错误。
        了解更多信息
        不再显示
favicon.ico:1   GET http://4.2.3.28:8080/favicon.ico 404 (Not Found)
```

