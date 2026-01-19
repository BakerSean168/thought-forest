---
tags:
  - status/growing
description: 国内服务器上，如果只安装了 Docker 而不配置镜像加速，你拉取镜像（比如 docker pull mysql）会非常慢甚至超时。
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---


# Docker镜像配置 

## 镜像源

### 阿里云


> [!NOTE] 注意
> 阿里镜像源好像已经停止同步最新镜像了


[容器镜像服务 ACR 控制台](https://cr.console.aliyun.com/cn-chengdu/instances/mirrors)
进入上面地址，直接使用下面的命令，整个复制和粘贴

[配置官方镜像加速器加速拉取Docker Hub镜像-容器镜像服务-阿里云](https://help.aliyun.com/zh/acr/user-guide/accelerate-the-pulls-of-docker-official-images?spm=5176.21213303.J_ZGek9Blx07Hclc3Ddt9dg.1.25862f3dNfQU3v&scm=20140722.S_help@@%E6%96%87%E6%A1%A3@@60750._.ID_help@@%E6%96%87%E6%A1%A3@@60750-RL_docker%E9%95%9C%E5%83%8F%E5%8A%A0%E9%80%9F%E5%99%A8-LOC_2024SPAllResult-OR_ser-PAR1_213e056717654504429801511e8a75-V_4-PAR3_o-RE_new5-P0_0-P1_0)


## 重启docker 使配置生效

```bash
systemctl daemon-reload
systemctl restart docker
```
