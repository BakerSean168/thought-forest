---
tags:
  - tech/ops/git
  - type/concept
  - status/growing
description: Git中的merge和rebase
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# merge and rebase


## `git merge` vs `git rebase`

| 特性       | git merge     | git rebase   |
| -------- | ------------- | ------------ |
| **提交历史** | 保留分支历史，产生合并提交 | 线性历史，重写提交历史  |
| **冲突处理** | 一次性解决所有冲突     | 逐个提交解决冲突     |
| **使用场景** | 功能分支合并到主分支    | 整理提交历史，同步主分支 |
| **协作影响** | 对已推送的提交安全     | 不要对已推送的提交使用  |

#### Merge 示例

```bash
git checkout main
git merge feature/login
# 产生一个合并提交
```

#### Rebase 示例

```bash
git checkout feature/login
git rebase main
# 将 feature/login 的提交重新应用到 main 之上
```


