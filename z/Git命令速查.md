---
tags:
  - tech/dev/git
  - type/howto
  - status/evergreen
description: Git 常用命令速查表
created: 2025-09-29T13:52:09
updated: 2025-12-07T17:00:00
---

# Git 命令速查

**上级索引**：[[Git MOC]] | [[Resource]]

[Pro Git book,这是一本关于如何使用 Git 的优秀图书](https://git-scm.com/book/en/v2)

## Git 常用命令

### 配置

| 命令                                                          | 说明            |
| ----------------------------------------------------------- | ------------- |
| `git config --global user.name "Your Name"`                 | 设置全局 Git 用户名  |
| `git config --global user.email "youremail@yourdomain.com"` | 设置全局 Git 用户邮箱 |
| `git config --list`                                         | 列出所有 Git 配置信息 |

*这些值保存在全局配置文件 ~/.gitconfig 中*

### 基本操作

| 命令                         | 说明                           |
| ---------------------------- | ------------------------------ |
| `git init`                   | 初始化一个新的 Git 仓库        |
| `git clone [url]`            | 克隆远程仓库                   |
| `git status`                 | 查看当前仓库状态               |
| `git add [file]`             | 添加文件到暂存区               |
| `git add .`                  | 添加所有文件到暂存区           |
| `git reset [file]`           | 取消暂存                       |
| `git commit -m "message"`    | 提交暂存区的文件               |
| `git push [remote] [branch]` | 推送本地分支到远程仓库         |
| `git pull`                   | 拉取远程仓库的更新并合并到本地 |
| `git pull --rebase`          | 拉取远程仓库的更新并在本地变基 |
| `git fetch`                  | 从远程仓库获取更新但不合并     |

### 分支操作

| 命令                                       | 说明          |
| ---------------------------------------- | ----------- |
| `git branch`                             | 列出所有本地分支    |
| `git branch -r`                          | 列出所有远程分支    |
| `git branch [branch-name]`               | 创建新分支       |
| `git checkout [branch-name]`             | 切换到指定分支     |
| `git checkout -b [branch-name]`          | 创建并切换到新分支   |
| `git merge [branch-name]`                | 合并指定分支到当前分支 |
| `git branch -d [branch-name]`            | 删除本地分支      |
| `git push origin --delete [branch-name]` | 删除远程分支      |

### 标签操作

| 命令                                  | 说明               |
| ------------------------------------- | ------------------ |
| `git tag`                             | 列出所有标签       |
| `git tag [tag-name]`                  | 创建新标签         |
| `git tag -d [tag-name]`               | 删除本地标签       |
| `git push origin [tag-name]`          | 推送标签到远程仓库 |
| `git push origin --delete [tag-name]` | 删除远程标签       |

### 查看历史

| 命令                     | 说明                         |
| ------------------------ | ---------------------------- |
| `git log`                | 查看提交历史                 |
| `git log --oneline`      | 查看简洁的提交历史           |
| `git log --graph`        | 查看图形化的提交历史         |
| `git diff`               | 查看工作区与暂存区的差异     |
| `git diff [branch-name]` | 查看当前分支与指定分支的差异 |

### 撤销操作

| 命令                                | 说明                                         | 使用场景          |
| --------------------------------- | ------------------------------------------ | ------------- |
| `git reset [file]`                | 撤销暂存区的文件                                   |               |
| `git reset --hard <commit-hash>`  | 重置工作区和暂存区到最后一次提交。                          | 把工作区和暂存区的文件删除 |
| `git reset --soft <commit-hash>`  | 重置到指定提交,但保留工作区和暂存区                         |               |
| `git reset --mixed <commit-hash>` | 重置到指定提交，保留工作区，清空暂存区                        |               |
| `git revert <commit-hash>`        | 撤销指定的提交（创建新提交来撤销指定提交）                      | 撤销上一次的提交      |
| `git rebase --abort`              | 取消变基操作                                     |               |
| `git revert HEAD --no-edit`       | 撤销上次的提交，并恢复；修改完代码提交，发现要求是另一个，使用此命令再重新修改并提交 |               |

### 远程仓库

| 命令                          | 说明                           |
| ----------------------------- | ------------------------------ |
| `git remote -v`               | 查看远程仓库信息               |
| `git remote add [name] [url]` | 添加远程仓库                   |
| `git remote remove [name]`    | 删除远程仓库                   |
| `git push [remote] [branch]`  | 推送本地分支到远程仓库         |
| `git pull [remote] [branch]`  | 拉取远程仓库的更新并合并到本地 |


## 版本信息

- **工具版本：**
- **最后更新：** 2025-09-29
