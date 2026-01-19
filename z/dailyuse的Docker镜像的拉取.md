---
tags: [tech/ops, type/debug, status/growing, type/howto]
description: 本文主要介绍如何在自己的服务圈上获取自己项目的 Docker 镜像。
tool: docker
created: 2025-12-12T21:31:47
updated: 2025-12-12T21:42:36
---

# dailyuse的Docker镜像拉取

## dockerfile 样例

正常情况下，就可以直接创建一个 Dockerfile 文件和对应的 env 文件，然后直接让 Docker Compose up 来拉取自己的项目镜像，并把 env 文件中的配置注入到 Dockerfile 文件中就可以了。 

```dockerfile
# version: '3.8'  现在不需要这个 version

services:
  api:
    image: ${REGISTRY}/${IMAGE_NAMESPACE}/dailyuse-api:${TAG}
    container_name: dailyuse-api
    restart: always
    environment:
      NODE_ENV: production
      # Use managed DB/Redis or deploy local ones
      DATABASE_URL: postgresql://${DB_USER}:${DB_PASSWORD}@postgres:5432/${DB_NAME}
      REDIS_URL: redis://:${REDIS_PASSWORD}@redis:6379
      LOG_LEVEL: info
    ports:
      - "${API_PORT}:3000"
    networks:
      - dailyuse-net
    depends_on:
      - postgres
      - redis

  web:
    image: ${REGISTRY}/${IMAGE_NAMESPACE}/dailyuse-web:${TAG}
    container_name: dailyuse-web
    restart: always
    # Expose an internal port; NPM will reverse-proxy to this
    ports:
      - "${WEB_PORT}:80"
    networks:
      - dailyuse-net
    depends_on:
      - api

  # Optional local infra; remove if you use managed services
  postgres:
    image: postgres:16-alpine
    container_name: dailyuse-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - dailyuse-net

  redis:
    image: redis:7-alpine
    container_name: dailyuse-redis
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD}
    restart: unless-stopped
    volumes:
      - redis-data:/data
    networks:
      - dailyuse-net

volumes:
  postgres-data:
  redis-data:

networks:
  dailyuse-net:
    driver: bridge
```

## 拉取失败的情况

对于国内来说，拉取失败是很正常的情况。尤其是，比如阿里云服务器，它本身的 Docker 竞选员已经是停滞维护了，也就是不会再同步更新 Docker 官方镜像源中新增的镜像。

所以说，我们新推送到 Docker 官方竞选员的镜像肯定也不会被收录，也就拉取不到。如果有条件的话，可以再给服务器配置代理。此外，我们也可以直接把本地的 Docker 镜像上传到我们的服务器中。 

```PowerShell
# 导出你的 API 镜像
docker save -o api.tar bakersean1688/dailyuse-api:v1.0.0

# 导出你的 Web 镜像
docker save -o web.tar bakersean1688/dailyuse-web:v1.0.0

# 顺便把 postgres 和 redis 也导出来 (因为服务器可能连它们也拉不下来)
docker pull postgres:16-alpine
docker save -o db.tar postgres:16-alpine

docker pull redis:7-alpine
docker save -o redis.tar redis:7-alpine


# **上传文件到服务器：** (假设你的服务器 IP 是 `1.2.3.4`)
scp *.tar root@1.2.3.4:/opt/dailyuse/


# 在服务器上导入镜像：
# 登录服务器后执行
cd /opt/dailyuse/
docker load -i api.tar
docker load -i web.tar
docker load -i db.tar
docker load -i redis.tar

#**启动：** 当镜像已经在本地（服务器）存在时，Docker Compose 就不会去网上拉了。
docker compose --env-file .env up -d

docker compose -f docker-compose.prod.yml --env-file .env up -d
```