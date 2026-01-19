---
tags:
  - tech/ops/git
  - type/howto
  - status/seed
description: git代码取消跟踪并移除
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# git代码取消跟踪并移除 

## 问题详情
## 解决方案

```

git status --porcelain

git rm -r --cached .venv
```

## 信息参考
