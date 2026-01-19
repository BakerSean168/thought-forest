---
tags:
  - tech/ops
  - type/debug
  - status/growing
description: ops
created: 2025-12-12T23:47:33
updated: 2025-12-12T23:48:05
tool: docker
---

# docker镜像更新后却没变化 

这是一个非常令人抓狂但又特别常见的问题！别担心，这大概率不是“灵异事件”，而是 Docker 的**分层缓存机制**或者**本地文件未保存**在作祟。

即使你用了 `docker build --no-cache`，如果源文件本身没变，或者你推送的步骤有误，服务器上拿到的依然是旧的。

请按照以下 4 个“排雷”步骤，彻底找出原因并解决它：

### 🕵️‍♂️ 第一步：灵魂拷问——本地文件真的改了吗？

很多时候，我们在编辑器里改了代码，但**没有保存**（或者保存到了错误的文件）。

1.  **检查本地文件**：打开你本地电脑上的 `nginx.conf`。
2.  **确认内容**：确认 `root /usr/share/nginx/html;` 这行代码，**是不是真的已经跑到了 `server {` 的第一层**（在所有 `location` 的外面）？
3.  **确认保存**：按 `Ctrl+S` 确保文件已保存。

### 🕵️‍♂️ 第二步：彻底的“焦土政策”重新构建

为了绝对排除缓存干扰，请完全照着下面这一套“组合拳”操作，**不要跳过任何一步**：

**1. 在本地 (Windows PowerShell) 清理旧产物：**

```powershell
# 删除本地旧的 web 镜像，防止 tag 混淆
docker rmi bakersean1688/dailyuse-web:v1.0.0
```

**2. 强制全新构建 (包含 --no-cache)：**

```powershell
# 注意：加上 --no-cache 强制不使用缓存
docker build --no-cache -f Dockerfile.web -t bakersean1688/dailyuse-web:v1.0.0 .
```

**3. 关键验证 (在推送前先检查)：**
在推送之前，我们先在**本地**验证一下生成的镜像对不对，免得白传。

```powershell
# 临时跑一个容器看看里面的配置
docker run --rm bakersean1688/dailyuse-web:v1.0.0 cat /etc/nginx/nginx.conf
```

  * **看输出**：这时候输出的内容里，`root` 移动位置了吗？
  * **如果不正确**：说明你的 Dockerfile `COPY` 的源文件路径不对，或者你改错文件了。
  * **如果正确**：继续下一步。

**4. 重新打包/推送：**

```powershell
# 导出新包
docker save -o web_new.tar bakersean1688/dailyuse-web:v1.0.0
```

*(或者如果你修好了网络直接 `docker push` 也可以)*

### 🕵️‍♂️ 第三步：服务器端的“除旧迎新”

上传 `web_new.tar` 到服务器后，操作也有讲究。Docker Load 有时候不会覆盖同名同 ID 的镜像，除非强制清理。

**在服务器上执行：**

**1. 停止并删除旧容器：**

```bash
cd /opt/dailyuse
docker compose down
```

**2. 暴力删除旧镜像 (必须做)：**
这一步是重点。如果不删，`docker load` 有时候会提示“镜像已存在”而跳过导入。

```bash
docker rmi bakersean1688/dailyuse-web:v1.0.0
```

**3. 导入新包：**

```bash
docker load -i web_new.tar
```

**4. 启动：**

```bash
docker compose --env-file .env up -d --force-recreate
```

### 🕵️‍♂️ 第四步：终极验证

容器启动后，再次检查服务器上的配置：

```bash
docker exec -it dailyuse-web cat /etc/nginx/nginx.conf
```

**如果这次 `root` 位置对了，你的 404 问题就会立刻消失！**

-----

### 💡 为什么之前会失败？可能性分析

1.  **最大的嫌疑人**：你本地修改了 `nginx.conf`，但是构建的时候，Docker 上下文（Context）里可能有多个 `nginx.conf`（比如根目录有一个，`apps/web` 下也有一个），Dockerfile `COPY` 了没修改的那个。
2.  **次要嫌疑人**：服务器上 `docker load` 时，因为标签一样且 ID 可能没变（如果你修改的文件极小，有时哈希碰撞或缓存层复用），Docker 认为没必要更新。**先 `rmi` 再 `load` 是最稳的。**

快试试这一套流程，特别是**本地构建完立刻用 `docker run ... cat ...` 检查**这一步，能帮你省下大量的上传时间！