---
tags:
  - tech/app/obsidian
  - tech/devops/git
  - type/howto
  - status/evergreen
description: Obsidian Git 版本控制与自动同步完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[同步与部署]]

---

# Obsidian Git 插件详细指南

> **核心功能**：Git 版本控制、自动提交、跨设备同步、冲突管理  
> **难度级别**：⭐⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：必装（同步需求）

---

## 📋 快速导航

- [功能概览](#功能概览)
- [安装与配置](#安装与配置)
- [基础使用](#基础使用)
- [自动同步](#自动同步)
- [冲突解决](#冲突解决)
- [进阶技巧](#进阶技巧)
- [常见问题](#常见问题)

---

## 功能概览

### 核心功能

| 功能 | 描述 | 适用场景 |
|-----|------|--------|
| **版本控制** | 追踪笔记变更历史 | 知识库备份、审计、回滚 |
| **自动提交** | 定时自动 Git 提交 | 多设备同步、防止丢失 |
| **冲突检测** | 检测并提示文件冲突 | 多设备修改同一文件 |
| **分支管理** | 切换与创建 Git 分支 | 版本管理、实验分支 |
| **远程同步** | 推送/拉取远程仓库 | 云端备份、跨设备同步 |
| **备份恢复** | 查看历史版本、恢复文件 | 误删文件恢复、版本回滚 |

### 与其他同步方案对比

| 方案 | 成本 | 复杂度 | 速度 | 隐私 | 推荐度 |
|-----|-----|-------|------|------|--------|
| **Obsidian Sync**（官方） | $96/年 | ⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Obsidian Git** | 免费 | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **iCloud / Google Drive** | 免费-付费 | ⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **手动 Git** | 免费 | ⭐⭐⭐⭐⭐ | - | ⭐⭐⭐⭐⭐ | ⭐⭐ |

---

## 安装与配置

### 前置条件

```
✅ Git 已安装（Windows/Mac/Linux）
✅ Obsidian 库是 Git 仓库（或将被初始化）
✅ Git 用户名与邮箱配置完成
```

**检查 Git 安装**：
```bash
git --version
git config --global user.name
git config --global user.email
```

**配置 Git 用户**（如未配置）：
```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

### 插件安装

1. **打开社区插件浏览器**
   - Settings → Community plugins → Browse

2. **搜索并安装**
   - 搜索 "Git"(作者: Vinzent03)
   - 点击 Install → Enable

3. **初始化 Git 仓库**（如未初始化）
   - 打开命令面板：Ctrl+P
   - 运行 "Obsidian Git: Initialize a new repo"
   - 等待初始化完成

### 初始配置

**Settings → Obsidian Git**

#### 基础设置

| 选项 | 默认 | 推荐值 | 说明 |
|-----|------|--------|------|
| **Commit message** | "vault backup" | `"{{date}}-{{numFiles}} files"` | 自定义提交信息 |
| **Commit author** | Git 配置 | 自动 | 使用全局 Git 配置 |
| **Auto pull interval** | disabled | 5 分钟 | 自动拉取远程更新 |
| **Auto push interval** | disabled | 10 分钟 | 自动推送本地更改 |
| **Auto backup interval** | disabled | 15 分钟 | 自动本地备份 |

#### 高级设置

```yaml
# 避免提交的文件/文件夹
Ignore patterns: |
  .DS_Store
  .obsidian
  .git
  node_modules
  *.tmp

# 只追踪指定文件
Tracked extensions: |
  md
  canvas
  yaml

# 大文件警告阈值（MB）
Large file warning threshold: 10
```

### 连接远程仓库

#### 方式 1：GitHub（推荐）

1. **在 GitHub 创建私有仓库**
   ```
   https://github.com/yourname/obsidian-vault
   ```

2. **克隆到本地**
   ```bash
   git clone https://github.com/yourname/obsidian-vault.git
   # 将已有库内容移入，或初始化新库
   ```

3. **添加远程源**
   ```bash
   cd /path/to/vault
   git remote add origin https://github.com/yourname/obsidian-vault.git
   git push -u origin main
   ```

4. **设置认证**
   - 使用 SSH 密钥（推荐）：
     ```bash
     git remote set-url origin git@github.com:yourname/obsidian-vault.git
     ```
   - 或使用 Personal Access Token（PAT）：
     - GitHub Settings → Developer settings → Personal access tokens
     - 创建 `repo` 权限的 token
     - 使用 token 作为密码认证

#### 方式 2：GitLab / Gitee

```bash
git remote set-url origin https://gitlab.com/yourname/obsidian-vault.git
# 或
git remote set-url origin https://gitee.com/yourname/obsidian-vault.git
```

#### 方式 3：自建 Git 服务器

```bash
# SSH 方式
git remote set-url origin ssh://user@server.com/path/to/vault.git

# HTTPS 方式
git remote set-url origin https://git.server.com/vault.git
```

---

## 基础使用

### UI 操作（推荐新手）

**打开 Git 面板**：
- 左边栏侧边栏 → Git 图标
- 或命令：Ctrl+P → "Obsidian Git: Open git panel"

**常用按钮**

| 按钮 | 功能 | 快捷键 |
|-----|------|--------|
| **Commit changes** | 提交本地更改 | Ctrl+K, Ctrl+C |
| **Pull remote** | 拉取远程更新 | Ctrl+K, Ctrl+P |
| **Push to remote** | 推送到远程 | Ctrl+K, Ctrl+S |
| **Open diff** | 查看变更差异 | - |

**工作流示例**

```
编辑笔记
  ↓
Git 面板显示"Changes"（文件数）
  ↓
写入提交信息（可选）
  ↓
点击"Commit changes"
  ↓
点击"Push to remote"
  ↓
完成！
```

### 命令操作

**常用命令**（命令面板 Ctrl+P）

```
Obsidian Git: Commit all changes
Obsidian Git: Pull remote changes
Obsidian Git: Push changes to remote
Obsidian Git: View file history
Obsidian Git: Blame line
Obsidian Git: Create new branch
Obsidian Git: Switch branch
```

### 查看历史

**方式 1：Graph View**
```
Settings → Community plugins → Obsidian Git
点击"Open diff"查看文件变更
```

**方式 2：命令行查看**
```bash
# 查看提交历史
git log --oneline

# 查看特定文件历史
git log --oneline -- notes/file.md

# 查看提交详情
git show <commit-hash>

# 查看文件变更
git diff <commit-hash>^ <commit-hash>
```

**方式 3：GitHub 网页查看**
```
在 GitHub 仓库页面查看：
- Commits：提交历史
- Pull requests：合并请求
- History：文件历史
```

---

## 自动同步

### 设置自动提交

**最小化配置**（推荐新手）
```
Auto backup interval: 15 minutes
Auto pull interval: 5 minutes
Auto push interval: 10 minutes
Commit message: "{{date}}-{{numFiles}} files"
```

**效果**：
- 每 15 分钟自动保存一次本地快照
- 每 5 分钟检查一次远程更新
- 每 10 分钟自动推送本地更改

### 跨设备同步流程

**场景**：在多台设备（电脑/平板）上编辑同一个库

```
设备 A (Mac)              设备 B (Windows)
  ↓                        ↓
编辑 Note 1              编辑 Note 2
  ↓                        ↓
自动提交到本地 Git          自动提交到本地 Git
  ↓                        ↓
自动推送到 GitHub          自动拉取 A 的更新
  ↓←─────────────────────→↓
        远程仓库同步
  ↓←─────────────────────→↓
自动拉取 B 的更新          自动推送到 GitHub
  ↓                        ↓
Note 1 + Note 2          Note 1 + Note 2
都在两台设备上             都在两台设备上
```

### 冲突避免策略

❌ **容易产生冲突的做法**
- 同时在两台设备编辑同一文件
- 编辑后不立即提交/推送
- 长时间不拉取远程更新

✅ **避免冲突的最佳实践**
- 在一台设备编辑完毕后再切换到另一台
- 启用自动推送与自动拉取
- 定期（每 5-10 分钟）同步一次

---

## 冲突解决

### 冲突的产生

**场景**：两台设备同时修改同一文件的同一行

```
设备 A 编辑 note.md：
    # My Note
    Content by A

设备 B 编辑 note.md：
    # My Note
    Content by B

拉取时产生冲突 ⚠️
```

### 检测冲突

**自动检测**：
- Git 面板显示 "⚠️ Conflict" 标记
- 问题面板 (Ctrl+Shift+J) 显示冲突文件

### 解决冲突（3 种方式）

#### 方式 1：手动编辑（完全控制）

**打开冲突文件**，看到类似内容：

```markdown
# My Note

<<<<<<< HEAD (Current Change)
Content by A
=======
Content by B
>>>>>>> branch-name (Incoming Change)
```

**操作**：
1. 保留需要的内容
2. 删除 `<<<<<<`, `======`, `>>>>>>` 标记
3. 保存文件

```markdown
# My Note

Content by A and B merged
```

4. 在 Git 面板点击"Commit changes"
5. 输入合并信息，如"Merge conflict resolved"

#### 方式 2：使用可视化工具

**安装 Git GUI 工具**：
```bash
# Mac
brew install gitk

# Windows
# 在安装 Git 时选择 Git GUI

# Linux
sudo apt-get install gitk
```

**操作**：
```bash
cd /path/to/vault
gitk  # 打开 Git 可视化工具
```

#### 方式 3：保留一方的版本

**Git 命令**：
```bash
# 保留当前分支的版本
git checkout --ours path/to/conflict/file.md

# 保留远程分支的版本
git checkout --theirs path/to/conflict/file.md

# 然后提交
git add .
git commit -m "Resolved conflict - kept ours/theirs"
```

### 冲突预防

**启用自动合并策略**：
```bash
git config --global merge.tool vimdiff
git config --global merge.conflictstyle diff3
```

**使用时间戳避免同步冲突**：
```yaml
# .obsidian/plugins/obsidian-git/settings.json
{
  "autoCommitMessage": "{{date}}-{{time}}-{{numFiles}} files",
  "autoBackupInterval": 5,
  "autoPullInterval": 2,
  "autoPushInterval": 3
}
```

---

## 进阶技巧

### 分支管理

**创建实验分支**

```bash
# 命令行创建
git branch experiment
git checkout experiment

# 或在 Git 面板操作
选择"Switch branch"→"Create new branch"
```

**用途**：
- 测试新笔记结构不影响主分支
- 保存多个版本

### 选择性同步

**只同步指定文件类型**

```yaml
# .obsidianignore
.DS_Store
.obsidian/
*.tmp
cache/
node_modules/

# 只追踪 Markdown 和 Canvas
tracked_extensions:
  - md
  - canvas
```

### 大文件处理

**使用 Git LFS（Large File Storage）**

```bash
# 安装 Git LFS
git lfs install

# 追踪大文件
git lfs track "*.pdf"
git lfs track "*.mp4"

# 提交
git add .gitattributes
git commit -m "Track large files with LFS"
```

### 清理历史

**压缩仓库大小**

```bash
# 查看占用空间大的文件
git rev-list --all --objects | sort -k2 | tail -20

# 清理本地缓存
git gc --aggressive

# 完全重写历史（危险）
git filter-branch --tree-filter 'rm -f path/to/large/file'
```

---

## 常见问题

### Q1：如何恢复已删除的文件？

```bash
# 查看删除历史
git log -- path/to/deleted/file.md

# 恢复到指定版本
git checkout <commit-hash> -- path/to/deleted/file.md

# 或在 Git 面板选择文件，点击"Restore"
```

### Q2：Git 面板消失了怎么办？

```
Toggle Git panel: Ctrl+K Ctrl+T
或命令：Obsidian Git: Toggle Git panel
```

### Q3：自动推送失败提示"authentication failed"？

**原因**：认证信息过期或配置不正确

**解决方案**：

1. **使用 SSH 密钥（推荐）**
   ```bash
   # 生成 SSH 密钥
   ssh-keygen -t ed25519 -C "your@email.com"
   
   # 复制公钥到 GitHub
   cat ~/.ssh/id_ed25519.pub
   # 粘贴到 GitHub Settings → SSH and GPG keys
   
   # 配置本地 Git
   git remote set-url origin git@github.com:username/vault.git
   ```

2. **使用 Personal Access Token**
   ```bash
   # GitHub: Settings → Developer settings → Tokens (classic)
   # 创建 repo 权限的 token
   
   # 配置
   git remote set-url origin https://username:token@github.com/username/vault.git
   ```

3. **缓存凭证**
   ```bash
   git config --global credential.helper cache
   # 凭证缓存 15 分钟
   ```

### Q4：库太大导致推送缓慢？

```bash
# 只推送最新的提交
git push --force-with-lease origin main

# 使用浅克隆（减小仓库大小）
git clone --depth=1 https://github.com/username/vault.git

# 清理 .obsidian 文件夹（插件配置，不必备份）
echo ".obsidian/" >> .gitignore
git rm -r --cached .obsidian/
git commit -m "Remove .obsidian from tracking"
```

### Q5：怎样在新设备上同步库？

```bash
# 第一台设备：推送到远程
git push -u origin main

# 第二台设备：克隆库
git clone https://github.com/username/vault.git
cd vault
# 在 Obsidian 中打开此文件夹

# 自动同步即开始
```

---

## 完整配置示例

**最优实践配置**（`.obsidian/plugins/obsidian-git/settings.json`）

```json
{
  "autoCommitMessage": "vault backup: {{date}}-{{time}} ({{numFiles}} files)",
  "autoBackupInterval": 10,
  "autoPullInterval": 5,
  "autoPushInterval": 10,
  "autoPushOnSave": true,
  "automaticallyPullOnOpen": true,
  "disablePush": false,
  "disablePull": false,
  "trackModifiedTime": true,
  "syncMethod": "merge",
  "gitPath": "",
  "basePath": "",
  "showStatusBar": true,
  "showCommunityPlugins": false,
  "timeFormat": "YYYY-MM-DD HH:mm:ss",
  "dateFormat": "YYYY-MM-DD",
  "blameMergeConflict": false,
  "changedFilesInStatusBar": false,
  "showedMobileNotice": false,
  "refreshSourceControl": true,
  "showBranchStatusBar": true,
  "showFileMenu": true,
  "showDiffButton": true,
  "textAreaUndoHistorySize": 0,
  "dontAskHowToHandle": false,
  "updateSubmodules": false,
  "gitIgnorePath": "",
  "showIgnoredFiles": false,
  "showUntrackedFiles": true,
  "isConfigPathOnly": false,
  "showAllBranchesInDropDownMode": false,
  "showCommitMessage": true,
  "showAuthor": true,
  "showCommitDate": true,
  "showLineAuthor": false,
  "showLineAuthorTime": false,
  "showLineAuthorTool": false,
  "showDeletedFiles": true,
  "showConflictFiles": true,
  "showSyncBranchIndicator": true,
  "showSubmodules": false,
  "splitDirection": "horizontal"
}
```

---

## 📚 相关文档

- [[Obsidian同步与部署指南]] - 整合 Git + Quartz + Digital Garden 的完整方案
- [[Obsidian Digital Garden插件详细指南]] - Web 发布与分享
- [[Quartz 4.0 静态部署指南]] - 静态网站生成

---

**上级索引**：[[Obsidian插件完全索引]] | [[同步与部署]]
