---
tags:
  - tech/ops/git
  - type/concept
  - status/seed
description: Git协作流程
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# 团队协作流程

1. **功能开发流程**

```bash
# 1. 拉取最新代码

# 切换到 main 分支
git checkout main 

# 拉取 main 分支的远程仓库中的代码
git pull origin main

# 2. 创建功能分支
git checkout -b feature/new-component

# 3. 开发完成后推送
git add .
git commit -m "feat: add new component"
git push origin feature/new-component

# 4. 创建 Pull Request
# 5. 代码审查和合并
```

手动合并分支
```bash
# 1. 获取远程最新引用
git fetch origin

# 2. 切换到 develop 并拉取最新（保证 develop 是最新）
git checkout develop
git pull origin develop

# 3. 合并 feature 分支到 develop（保留合并记录）
git merge --no-ff feature/your-branch -m "Merge feature/your-branch into develop"

# 如果你想把多个提交合并为一个提交（squash）
# git merge --squash feature/your-branch
# git commit -m "Squash merge feature/your-branch into develop"

# 4. 若出现冲突，查看冲突文件，手动编辑解决
git status
# 编辑有冲突的文件，解决后：
git add <resolved-file1> <resolved-file2>
git commit   # 完成合并提交（如果 merge 自动产生 commit 则可能不需要这一步）

# 如果想放弃合并并回到合并前状态
# git merge --abort

# 5. 本地验证（示例：安装依赖/构建/测试）
# 查看 package.json 可用脚本：[package.json](package.json)
npm install
npm run build
npm test

# 6. 推送 develop 到远程
git push origin develop

# 7. 可选：删除本地和远程 feature 分支（合并完成后）
git branch -d feature/your-branch
git push origin --delete feature/your-branch
```


2. **热修复流程**

```bash
# 从 main 分支创建热修复分支
git checkout main
git checkout -b hotfix/critical-bug

# 修复后直接合并到 main 和 develop
git checkout main
git merge hotfix/critical-bug
git checkout develop
git merge hotfix/critical-bug
```

## 本地拉取分支

```bash
PS D:\myPrograms\DailyUse> git pull origin main
From github.com:BakerSean168/DailyUse
 * branch              main       -> FETCH_HEAD
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint:
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```

这个提示说明你的本地分支和远程 main 分支有分歧（divergent branches）。这通常发生在本地和远程都有各自的提交时。

```bash
# 1. 查看当前状态
git status

# 2. 确保当前工作区是干净的
# 如果有未提交的改动：
git stash  # 暂存改动

# 3. 配置 pull 策略为 merge
git config pull.rebase false

# 4. 拉取并合并 main
git pull origin main

# 5. 如果有冲突，Git 会提示你解决冲突
# 打开冲突文件，手动解决冲突标记：
# <<<<<<< HEAD
# 你的改动
# =======
# main 的改动
# >>>>>>> origin/main

# 6. 解决冲突后，标记为已解决
git add .

# 7. 完成合并
git commit -m "Merge origin/main into feature/account-authentication-implementation"

# 8. 如果之前用了 stash，恢复改动
git stash pop

# 9. 推送到远程
git push origin feature/account-authentication-implementation
```
