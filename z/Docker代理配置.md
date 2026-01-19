---
tags:
  - tech/ops
  - type/howto
  - status/growing
description: Docker 对国内网络十分不友好，需要配置代理才能正常访问。
tool: docker
---
# Docker代理配置 

## Windows

直接在 setting - resources - Proxies 中配置代理


> [!NOTE] 注意
> 1. windows 上一般是使用 Docker Desktop 桌面应用，本质上 docker 应该是依赖 wsl 运行，所以应该使用 `http://host.docker.internal:7890`（指向主机） 而非 `http://localhost:7890`（指向wsl）， wsl 网络为 mirror 就不需要。
> 2. 要让代理客户端 **允许局域网连接**
> 3. 关闭防火墙（用于测试），确认是防火墙问题后，可以专门给代理软件添加入站规则
> 4. 直接dockerfile 构建时报错，可以先试试单独拉取镜像


注意事项



