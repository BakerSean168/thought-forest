---
tags:
  - tech/ops/git
  - type/howto
  - status/evergreen
description: 迁移git仓库变更
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# 迁移git仓库变更 

## 情境

之前进行业务实现时，让我从部署的服务器取源码，然后更改，现在发现其实有建立 git 仓库。
现在需要把之前的修改应用到 已有的公司的 git 仓库中。

最常用的方法是使用 `git diff` 命令将这些变更输出为一个 `.patch` 文件。

## 使用 git diff

你需要打开终端或命令行，进入这个 `bigbluebutton` 仓库的根目录来执行 Git 命令。

### 1\. 暂存所有变更

补丁文件通常是基于**暂存区（Staging Area）的，或者是暂存区和最新提交之间的差异。为了确保所有你在图片中看到的工作区**变更都被包含，你需要先将它们全部暂存起来。

```bash
git add .
```

  * **注意：** 如果你只想打包**部分**文件，请只 `git add` 你需要的文件。

### 2\. 生成补丁文件

接下来，使用 `git diff` 命令，结合 `$HEAD`（最新提交）和 `--cached`（暂存区）来生成补丁文件。

```bash
git diff HEAD --cached > my_changes.patch
```

  * **`git diff HEAD --cached`**: 比较**最新提交** (`HEAD`) 和当前的**暂存区** (`--cached`) 之间的差异。这个差异就是你即将提交但尚未提交的全部变更。
  * **`> my_changes.patch`**: 将差异输出重定向到一个名为 `my_changes.patch` 的文件中。

### 3. 应用补丁

将生成的 `my_changes.patch` 文件复制到另一个一模一样的仓库的根目录，然后执行以下命令：
```bash
git apply my_changes.patch
```



## ↩️ 撤销 `git apply` 的补丁

要撤销一个已经应用到工作区的补丁文件（例如 `my_changes.patch`），请在相同的 Git 仓库目录下运行以下命令：

```bash
git apply --reverse my_changes.patch
```

### 📋 命令说明

  * **`git apply`**: 这是应用和撤销补丁的主要命令。
  * **`--reverse`**: 这个选项告诉 Git，不要应用补丁文件中描述的更改，而是应用其**逆向**的更改，从而达到撤销原始更改的目的。
  * **`my_changes.patch`**: 你用来应用更改的原始补丁文件。

### ⚠️ 注意事项

1.  **工作区状态：** 在执行撤销操作前，确保工作区和暂存区没有其他未提交的、与补丁文件冲突的更改。
2.  **成功应用的前提：** 只有当 `git apply` 命令**成功地**将更改应用到工作区时，`--reverse` 才能将其撤销。
3.  **如果补丁已提交：** 如果你应用了补丁，并且已经将这些更改通过 `git commit` 提交到了历史记录中，那么你需要使用 `git revert` 或 `git reset` 来撤销提交，而不是 `git apply --reverse`。

**简而言之，`git apply --reverse` 专门用于撤销** **`git apply`** **操作本身，且只作用于工作区的文件变更。**

## 知识


