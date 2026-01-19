---
tags:
  - tech/ops
  - type/debug
  - status/growing
  - type/howto
description: ops
tool: docker
created: 2025-12-12T21:31:47
updated: 2025-12-12T21:33:10
---

# dailyuse的Docker镜像推送 

`docker tag docker.io/dailyuse/dailyuse-api:v1.0.0 docker.io/bakersean1688/dailyuse-api:v1.0.0`
给镜像打标签，改名

`docker push docker.io/bakersean1688/dailyuse-api:v1.0.0`

- bakersean1688 是命名空间，要使用自己的名字