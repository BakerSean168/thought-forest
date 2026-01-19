---
tags:
  - status/growing
  - type/concept
  - tech/dev
description: base_template
created: 2025-12-18T21:36:49
updated: 2025-12-18T21:37:05
---

# GitLab Flow 

## Overview

- **提出者：** **GitLab 官方**
- **起源：** 为了解决 GitHub Flow 过于简单（无法应对复杂的部署环境）和 Gitflow 过于复杂的问题，GitLab 提出了这个折中方案。
- **核心逻辑：** 它是 GitHub Flow 的增强版，引入了**“上游优先” (Upstream First)** 的原则。
    - 它允许在 `master` 之外建立**环境分支**（如 `production`, `pre-production`）或**版本分支**（如 `stable-1.0`）。
    - 代码必须先合并到 `master`，经过测试后，再 cherry-pick 或 merge 到下游的环境分支中。
- **适用场景：** 需要部署到多个环境（测试、预发、生产）的公司，或者需要维护多个旧版本的软件。