---
tags: [type/howto, status/evergreen, tech/ops]
description: git差异法，用于在开源项目上修改、部署后。在不手动对比成千上万个文件的情况下，从 6.9G 的“脏”目录中，提取出真正有价值的“修改过的代码”，并清洗掉依赖和垃圾文件。
tool: git
created: 2025-12-17T18:17:16
updated: 2025-12-17T22:30:49
---
## 总结

核心指令： **rsync**

先通过 commitID 找到 官方源码的 相同时间线。此时 脏仓库 - 新仓库 = 修改过的代码 + 依赖 + 垃圾文件
再用 --exclude 排除 依赖 + 垃圾文件

## 概览

这是一个典型的“先上车后补票”场景（Technical Debt），在快速迭代的初创项目中非常常见。

你现在的核心痛点是：**如何在不手动对比成千上万个文件的情况下，从 6.9G 的“脏”目录中，提取出真正有价值的“修改过的代码”，并清洗掉依赖和垃圾文件。**

我推荐使用 **“Git 差异覆盖法”**。这种方法利用 Git 本身强大的文件对比功能，帮你自动识别出哪些文件是原生的，哪些是你修改过的。

以下是分步操作指南：

## 步骤

### 第一步：准备工作与安全备份

在进行任何操作之前，请务必对现有目录进行备份，以防误操作。

```bash
# 假设你的生产目录是 /home/boc-live/bbb-docker-3.0.8-release/
cd /home/boc-live/
tar -czvf bbb-docker-backup.tar.gz bbb-docker-3.0.8-release/

```

---

### 第二步：建立“干净”的基准环境

我们需要先拉取官方的一份纯净代码，作为对比的基准（Baseline）。

1. **创建一个新的工作目录**（不要在原目录操作）：
```bash
mkdir /home/boc-live/bbb-migration
cd /home/boc-live/bbb-migration

```


2. **拉取官方代码并切到对应版本**：
你需要找到当时部署的 `bbb-docker` 的官方 GitHub 地址。
```bash
# 克隆官方仓库
git clone https://github.com/bigbluebutton/bbb-docker.git
cd bbb-docker

# 这一步非常重要：切到和你生产环境一致的 tag (v3.0.8)
git checkout v3.0.8

# 可以通过 
git describe --tags
# 输出示例：v3.0.8 (这就是精准版本) 
# 输出示例：v3.0.8-4-g9a2b3c (代表基于 v3.0.8 修改了4次提交)

# 2. 如果上面命令报错，试试查看最近一次提交的哈希值 
git log -1 
# 记下 commit id (例如 a1b2c3d)，然后去官方仓库搜这个 ID

git show <COMMIT_ID>  git show 45d13f46cc21c4e0656f6fd112108ff23014e054


git checkout main

git reset --hard 45d13f46cc21c4e0656f6fd112108ff23014e054
```


3. **重置 Git 远程仓库**：
现在的远程是官方的，你需要把它改成你们公司自己的私有仓库地址。
```bash
# 移除官方源
git remote remove origin

# 添加你们的私有仓库地址
git remote add origin <你的私有Git仓库地址>

```



---

### 第三步：使用 Rsync 进行“覆盖”与“过滤”

现在我们把生产环境那个 6.9G 的目录里的文件，**覆盖**到这个干净的目录里。关键在于利用 `rsync` 排除掉那些巨大的垃圾文件（如 `node_modules`, `logs`, 数据库文件等）。

```bash
# 请仔细检查路径，确保源目录和目标目录正确
# --exclude 用于排除不需要同步的大文件夹
# --dry-run 是预演模式，不会真正复制，先跑一次看看会同步什么

rsync -av --dry-run \
--exclude='.git' \
--exclude='node_modules' \
--exclude='logs' \
--exclude='data' \
--exclude='tmp' \
--exclude='*.log' \
--exclude='dist' \
/home/boc-live/bbb-docker-3.0.8-release/  /home/migration-bbb/boc-live-caption/

```

**注意：**

* 检查输出列表。如果看到了大量的 `.jar`, `.so`, 或者不应该出现的二进制大文件，继续在上面添加 `--exclude` 规则。
* 确认无误后，去掉 `--dry-run` 真正执行覆盖。

```bash
# 执行覆盖
rsync -av \
--exclude='.git' \
--exclude='node_modules' \
--exclude='logs' \
--exclude='data' \
--exclude='tmp' \
--exclude='*.log' \
--exclude='dist' \
/home/boc-live/bbb-docker-3.0.8-release/  /home/boc-live/bbb-migration/bbb-docker/

```

---

### 第四步：利用 Git 识别修改 (核心步骤)

现在，你的 `/home/boc-live/bbb-migration/bbb-docker/` 目录里包含了：

1. 官方的原始代码（Git 历史）。
2. 你们生产环境覆盖过来的修改。

使用 Git 的状态命令，它会告诉你发生了什么：

```bash
git status

```

**这时候你会看到三种状态的文件：**

1. **Modified (红色):** 这就是你们修改过的核心源码！(重点关注)
2. **Untracked (红色):** 这可能是你们新增的功能文件，也可能是漏掉没排除的垃圾文件。
3. **Clean (无显示):** 没动过的官方文件。

### 第五步：清洗与提交

1. **处理 .gitignore**：
针对 Untracked 文件中你不想要的东西（比如编译产生的 `target/` 目录，或者残留的 `.zip`），编写或更新 `.gitignore` 文件。
```bash
vim .gitignore
# 把不需要的大目录加进去

```


2. **暂存并提交**：
```bash
# 把所有修改和新增文件加入暂存区
git add .

# 再次检查，确保没有把几百兆的大文件加进去
git status 

# 提交
git commit -m "Initial import: Sync from production environment (based on v3.0.8)"

```


3. **推送到私有仓库**：
```bash
git push -u origin master
# 或者 main，取决于你的分支名

```



---

### 额外建议：如何处理 6.9G 的原因

那个 6.9G 的目录之所以大，大概率是因为 `bbb-docker` 使用 docker-compose 时，将数据卷（Volume）挂载到了当前目录下，或者构建过程中的中间产物没有清理。

在整理完代码后，建议检查目录下的 `docker-compose.yml` 文件：

1. **检查 Volumes**：查看是否类似 `./data:/var/lib/postgresql` 这样的映射。如果有，这些 `./data` 目录绝对不能提交到 Git。
2. **检查 Docker 镜像**：如果你们修改了代码，是否需要重新 build 镜像？
* 如果以前是直接改容器里的文件（极不推荐），你需要把这些文件提取出来，写成 `Dockerfile` 的 `COPY` 指令，或者通过 Volume 挂载进去。

## 实战

### 有子模块的仓库

1. 本地主机 克隆 官网 bbb-docker 项目（仓库1）
2. 利用 commitID 把 仓库1 恢复到和 生产环境项目（仓库2） 版本一致
3. 然后把 仓库1 的远程仓库 从官网 改为 公司gitlab（仓库3） 并推送
4. 再在 公司服务器上（仓库4） 拉取 仓库3 代码
5. 再用 仓库2 的代码 rsync 到 仓库4
6. 提交 仓库4 的新代码，推送到 仓库3

此时发现 仓库3 的子模块都是 其他官网仓库的链接，仓库 4 的子模块文件夹都是空的

**应该先把仓库中的子模块的移除 [[git子模块移除]]**

### 总结下一步

通过以上步骤，你就能得到一个干净的、基于官方 v3.0.8 但包含你们所有修改的 Git 仓库。

**你可以做的下一步：**
如果 `git status` 显示了太多 Untracked 的文件，让你眼花缭乱，你可以把 `git status` 的结果复制一部分发给我，我帮你写一个精准的 `.gitignore` 文件。


rsync -av \
--dry-run \
--exclude='.git' \
--exclude='node_modules' \
--exclude='ws-proxy/node_modules' \
--exclude='logs' \
--exclude='data' \
--exclude='tmp' \
--exclude='*.log' \
--exclude='dist' \
--exclude='*.o' \
--exclude='*.lo' \
--exclude='*.la' \
--exclude='.libs' \
--exclude='*/resources/raw' \
/home/boc-live/bbb-docker-3.0.8-release/  /home/migration-bbb/boc-live-caption/