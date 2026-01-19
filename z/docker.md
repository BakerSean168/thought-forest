---
tags:
  - tech/dev/docker
  - type/howto
  - status/growing
description: Docker 命令速查表
created: 2025-09-29T14:01:30
updated: 2025-12-07T17:00:00
---

# Docker 命令速查

**上级索引**：[[Docker MOC]] | [[Resource]]

## 镜像查找

[docker镜像查找](https://hub.docker.com/)

## 命令

### 基础

| 命令                                      | 说明                   |
| --------------------------------------- | -------------------- |
| `docker pull [image]`                   | 从 Docker 仓库中拉取镜像     |
| `docker images`                         | 列出本地的所有镜像            |
| `docker run [options] [image]`          | 运行一个容器               |
| `docker stop [container]`               | 停止一个运行中的容器           |
| `docker start [container]`              | 启动一个容器               |
| `docker restart [container]`            | 重启一个容器               |
| `docker ps [-a]`                        | 列出当前运行（所有）的容器        |
| `docker rm [container]`                 | 删除一个停止的容器            |
| `docker rmi [image]`                    | 删除一个镜像               |
| `docker exec -it [container] /bin/bash` | 进入一个运行中的容器           |
| `docker-compose up -d`                  | 启动 Docker Compose 服务 |
| `docker-compose down`                   | 停止 Docker Compose 服务 |
| `docker stop $(docker ps -aq)`          | 停止所有的容器              |
| `docker rm $(docker ps -aq)`            | 删除所有的容器              |
| `docker container prune -f`             | 删除所有停止的容器            |
| `docker image prune -f -a`              | 删除所有不使用的镜像           |
| `docker rmi $(docker images -q)`        | 删除所有的镜像              |

### 重启策略

| 命令            | 作用                                      |
| --------------- | ----------------------------------------- |
| `no`            | 默认策略，在容器退出时不重启容器          |
| `on-failure`    | 在容器非正常退出时（退出状态非0），才会重启容器 |
| `on-failure:3`  | 在容器非正常退出时重启容器，最多重启3次   |
| `always`        | 在容器退出时总是重启容器                  |
| `unless-stopped`| 在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器 |

运行一个容器时添加策略  

```docker run -d --restart 策略 容器```

已经运行的容器添加策略  

```docker container update --restart 策略 容器```

查看容器的重启策略  

```docker inspect -f "{{ .HostConfig.RestartPolicy.Name }}" 容器```

查看容器启动次数命令  

```docker inspect -f "{{ .RestartCount }}" 容器```

查看容器最后一次启动时间  

```docker inspect -f "{{ .State.StartedAt }}" 容器```

## 高级用法

### 快捷键

| 快捷键 | 功能 | 说明 |
| ------ | ---- | ---- |
|        |      |      |
|        |      |      |
|        |      |      |

### 配置选项

| 选项 | 默认值 | 说明 |
| ---- | ------ | ---- |
|      |        |      |
|      |        |      |
|      |        |      |

## 版本信息

- **工具版本：**
- **最后更新：** 2025-09-29
