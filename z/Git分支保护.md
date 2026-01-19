---
tags:
  - tech/ops/git
  - type/concept
  - status/seed
description: Git分支保护
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# Git 分支保护指南

## 📋 基础概念

### 什么是分支保护？

分支保护（Branch Protection）是 Git 托管平台（如 GitHub、GitLab）提供的一种安全机制，用于防止对重要分支的不当操作，确保代码质量和团队协作的规范性。

### 为什么需要分支保护？

#### 1. **防止意外破坏**

- ❌ 防止直接推送未经审查的代码
- ❌ 防止强制推送覆盖历史记录
- ❌ 防止误删重要分支

#### 2. **保证代码质量**

- ✅ 强制代码审查流程
- ✅ 确保自动化测试通过
- ✅ 要求状态检查完成

#### 3. **规范团队协作**

- 📋 统一工作流程
- 👥 明确审查责任
- 🔄 标准化合并流程

#### 4. **追溯和审计**

- 📝 保留完整的变更记录
- 👀 记录审查者和批准者
- 🕒 追踪代码变更历史

### 核心功能

```
┌─────────────────────────────────────────┐
│         分支保护规则                     │
├─────────────────────────────────────────┤
│                                         │
│  🔒 合并限制                             │
│    ├─ 要求 Pull Request                 │
│    ├─ 要求代码审查                       │
│    └─ 要求审查批准                       │
│                                         │
│  ✅ 状态检查                             │
│    ├─ CI/CD 构建通过                     │
│    ├─ 测试覆盖率达标                     │
│    ├─ 代码检查通过                       │
│    └─ 分支保持最新                       │
│                                         │
│  🚫 推送限制                             │
│    ├─ 禁止强制推送                       │
│    ├─ 禁止删除分支                       │
│    └─ 限制推送权限                       │
│                                         │
│  👥 权限管理                             │
│    ├─ 管理员豁免                         │
│    ├─ 允许绕过规则                       │
│    └─ 签名提交要求                       │
│                                         │
└─────────────────────────────────────────┘
```

### 保护级别

| 级别 | 分支类型 | 保护强度 | 适用场景 |
|------|---------|---------|---------|
| **严格** | `main` | 🔴 最高 | 生产环境代码 |
| **标准** | `dev` | 🟡 中等 | 开发集成分支 |
| **宽松** | `feature/*` | 🟢 较低 | 功能开发分支 |
| **无保护** | 个人分支 | ⚪ 无 | 个人实验分支 |

## 🛠️ 使用指南

### GitHub 分支保护配置

#### 第一步：访问设置页面

```
1. 打开 GitHub 仓库
2. 点击 "Settings" (设置)
3. 在左侧菜单选择 "Branches" (分支)
4. 点击 "Add branch protection rule" (添加分支保护规则)
```

**直达链接**: `https://github.com/BakerSean168/DailyUse/settings/branches`

#### 第二步：配置 main 分支保护

##### 1. 基础设置

```yaml
Branch name pattern: main
```

##### 2. 合并要求 (Protect matching branches)

**✅ Require a pull request before merging**
- 要求通过 Pull Request 才能合并
- 防止直接推送代码

配置子选项：

```
☑️ Require approvals: 1
   └─ 至少需要 1 人审查批准
   
☑️ Dismiss stale pull request approvals when new commits are pushed
   └─ 新提交后，旧的审查批准失效
   
☑️ Require review from Code Owners
   └─ 需要代码所有者审查（可选）
   
☑️ Require approval of the most recent reviewable push
   └─ 要求最新推送获得批准
```

**✅ Require status checks to pass before merging**
- 要求状态检查通过才能合并

配置子选项：

```
☑️ Require branches to be up to date before merging
   └─ 合并前分支必须是最新的
   
选择必需的状态检查：
☑️ CI Build
☑️ Tests
☑️ TypeScript Check
☑️ Lint
☑️ Format Check
```

**✅ Require conversation resolution before merging**
- 要求所有讨论都已解决

**✅ Require signed commits**
- 要求签名提交（可选，增强安全性）

**✅ Require linear history**
- 要求线性历史（可选，禁止合并提交）

##### 3. 推送限制

**☑️ Require deployments to succeed before merging**
- 要求部署成功（生产环境推荐）

**☑️ Lock branch**
- 锁定分支为只读（极端保护，不推荐）

##### 4. 规则应用

**✅ Do not allow bypassing the above settings**
- 管理员也不能绕过规则

**可选配置**：

```
☐ Allow force pushes
   └─ 允许强制推送（不推荐）
   
☐ Allow deletions
   └─ 允许删除分支（不推荐）
```

##### 5. 完整配置示例（main）

```yaml
分支保护规则 - main:
  ✅ Require pull request before merging
     ├─ Required approvals: 1
     ├─ Dismiss stale approvals: ✅
     ├─ Require approval of most recent push: ✅
     └─ Restrict who can dismiss reviews: ❌
  
  ✅ Require status checks to pass
     ├─ Require branches to be up to date: ✅
     ├─ Status checks:
     │  ├─ CI Build
     │  ├─ Tests
     │  ├─ TypeScript Check
     │  └─ Lint
     
  ✅ Require conversation resolution: ✅
  ✅ Require signed commits: ❌ (可选)
  ✅ Require linear history: ❌ (可选)
  
  ❌ Allow force pushes: ❌
  ❌ Allow deletions: ❌
  
  ✅ Do not allow bypassing: ✅
```

#### 第三步：配置 dev 分支保护

dev 分支的保护可以相对宽松一些：

```yaml
分支保护规则 - dev:
  ✅ Require pull request before merging
     ├─ Required approvals: 1 (或 0，团队内部可商量)
     ├─ Dismiss stale approvals: ✅
     └─ Require approval of most recent push: ✅
  
  ✅ Require status checks to pass
     ├─ Require branches to be up to date: ✅
     ├─ Status checks:
     │  ├─ CI Build
     │  ├─ Tests
     │  └─ Lint (可选更少的检查)
  
  ✅ Require conversation resolution: ✅
  
  ❌ Allow force pushes: ❌
  ❌ Allow deletions: ❌
  
  ⚠️ Allow bypass: 可以允许团队负责人绕过
```

#### 第四步：保存并测试

```bash
# 1. 保存配置后，尝试直接推送到 main（应该失败）
git checkout main
git push origin main
# 错误: 受保护的分支

# 2. 正确流程：通过 PR 合并
git checkout dev
git checkout -b feature/test-protection
# ... 做一些修改 ...
git push origin feature/test-protection
# 在 GitHub 创建 PR: feature/test-protection -> dev
```

### 不同场景的配置方案

#### 方案 1: 严格保护（适合成熟团队）

```yaml
main:
  合并要求:
    - 需要 PR: ✅
    - 审查人数: 2+ 人
    - 状态检查: 全部通过
    - 对话解决: ✅
    - 签名提交: ✅
  
  推送限制:
    - 强制推送: ❌
    - 删除分支: ❌
    - 绕过规则: ❌

dev:
  合并要求:
    - 需要 PR: ✅
    - 审查人数: 1+ 人
    - 状态检查: 核心测试
    - 对话解决: ✅
  
  推送限制:
    - 强制推送: ❌
    - 删除分支: ❌
```

#### 方案 2: 平衡保护（适合中小团队）

```yaml
main:
  合并要求:
    - 需要 PR: ✅
    - 审查人数: 1+ 人
    - 状态检查: CI + Tests
    - 对话解决: ✅
  
  推送限制:
    - 强制推送: ❌
    - 删除分支: ❌
    - 绕过规则: 管理员可绕过

dev:
  合并要求:
    - 需要 PR: ✅
    - 审查人数: 可选
    - 状态检查: CI Build
  
  推送限制:
    - 强制推送: ❌
```

#### 方案 3: 基础保护（适合个人或小团队）

```yaml
main:
  合并要求:
    - 需要 PR: ✅
    - 审查人数: 0（自己审查）
    - 状态检查: CI Build
  
  推送限制:
    - 强制推送: ❌
    - 删除分支: ❌

dev:
  合并要求:
    - 需要 PR: 可选
  
  推送限制:
    - 强制推送: ❌
```

### DailyUse 项目推荐配置

根据你的项目情况，推荐使用**方案 2（平衡保护）**：

#### main 分支配置

```bash
# 访问配置页面
https://github.com/BakerSean168/DailyUse/settings/branch_protection_rules/new

# 配置内容：
Branch name pattern: main

☑️ Require a pull request before merging
   ├─ Required approvals: 1
   ├─ Dismiss stale pull request approvals: ✅
   └─ Require approval of the most recent push: ✅

☑️ Require status checks to pass before merging
   ├─ Require branches to be up to date: ✅
   └─ Status checks required:
      ├─ build (如果有 CI)
      └─ test (如果有 CI)

☑️ Require conversation resolution: ✅

☑️ Do not allow bypassing the above settings
   └─ 管理员也不能绕过（可根据实际调整）

❌ Allow force pushes: disabled
❌ Allow deletions: disabled
```

#### dev 分支配置

```bash
Branch name pattern: dev

☑️ Require a pull request before merging
   └─ Required approvals: 0 或 1（根据团队规模）

☑️ Require status checks to pass before merging
   ├─ Require branches to be up to date: ✅
   └─ Status checks required:
      └─ build (基础检查)

❌ Allow force pushes: disabled
❌ Allow deletions: disabled
```

### 验证分支保护是否生效

```bash
# 测试 1: 尝试直接推送到 main
git checkout main
echo "test" >> test.txt
git add test.txt
git commit -m "test: direct push"
git push origin main
# 预期结果: ❌ 被拒绝

# 测试 2: 通过 PR 流程
git checkout dev
git checkout -b feature/test-branch-protection
echo "test" >> test2.txt
git add test2.txt
git commit -m "feat: test branch protection"
git push origin feature/test-branch-protection
# 在 GitHub 创建 PR
# 预期结果: ✅ 可以创建 PR，但需要满足保护规则才能合并

# 测试 3: 尝试强制推送
git push -f origin main
# 预期结果: ❌ 被拒绝

# 测试 4: 尝试删除分支
git push origin --delete main
# 预期结果: ❌ 被拒绝
```

## 📚 最佳实践

### 1. 分支保护策略

#### 根据重要性分级

```
生产分支 (main):
  🔴 最高保护级别
  ├─ 多人审查
  ├─ 完整测试
  ├─ 部署验证
  └─ 不可绕过

开发分支 (dev):
  🟡 标准保护级别
  ├─ 单人审查
  ├─ 基础测试
  └─ 管理员可绕过

功能分支 (feature/*):
  🟢 最低保护级别
  ├─ 可选审查
  ├─ 自行管理
  └─ 灵活操作
```

#### 保护规则优先级

```
1. 防止数据丢失 > 流程便利性
2. 代码质量 > 开发速度
3. 团队协作 > 个人便利
4. 自动化检查 > 人工把关
```

### 2. Code Review 要求

#### 审查人数建议

| 项目阶段 | main 分支 | dev 分支 | 说明 |
|---------|----------|---------|------|
| 初创期 | 0-1 人 | 0 人 | 快速迭代 |
| 成长期 | 1 人 | 0-1 人 | 平衡质量与速度 |
| 成熟期 | 2+ 人 | 1 人 | 保证质量 |
| 大型项目 | 2-3 人 | 1-2 人 | 多重把关 |

#### 审查质量标准

**Code Owner 机制**：

```
# .github/CODEOWNERS 示例

# 所有代码默认所有者
* @BakerSean168

# API 模块
/apps/api/ @backend-team

# Web 模块
/apps/web/ @frontend-team

# 核心域模型
/packages/domain-*/ @architecture-team

# 文档
/docs/ @all-team
```

### 3. 状态检查配置

#### 必需的状态检查

```yaml
# .github/workflows/pr-checks.yml
name: PR Checks

on:
  pull_request:
    branches: [main, dev]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: pnpm install
      - name: Run lint
        run: pnpm lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: pnpm install
      - name: Run tests
        run: pnpm test

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: pnpm install
      - name: Build
        run: pnpm build
```

#### 状态检查优先级

```
1. 🔴 必须通过（阻止合并）:
   ├─ Build 构建
   ├─ Unit Tests 单元测试
   └─ Type Check 类型检查

2. 🟡 建议通过（警告）:
   ├─ Lint 代码检查
   ├─ Format 格式检查
   └─ Coverage 覆盖率

3. 🟢 可选通过（参考）:
   ├─ Performance 性能测试
   ├─ E2E Tests E2E测试
   └─ Security Scan 安全扫描
```

### 4. 紧急情况处理

#### 临时绕过保护规则

**场景**: 生产环境紧急修复

```bash
# 方案 1: 管理员临时允许绕过（推荐）
1. GitHub Settings → Branches → 编辑 main 保护规则
2. 临时勾选 "Allow specified actors to bypass"
3. 完成修复后立即恢复设置

# 方案 2: 使用 hotfix 流程（推荐）
1. 从 main 创建 hotfix 分支
2. 快速修复并创建 PR
3. 快速审查（可以是自己）
4. 合并 PR 并打 tag

# 方案 3: 命令行强制（不推荐）
# 需要临时禁用保护规则，非常不建议
```

#### 权限管理

```
角色权限建议:

管理员 (Admin):
  ✅ 配置分支保护规则
  ✅ 管理仓库设置
  ✅ 紧急情况绕过（需要记录）
  ✅ 强制推送（极少使用）

维护者 (Maintainer):
  ✅ 审查和合并 PR
  ✅ 管理 Issue 和 PR
  ❌ 修改保护规则
  ❌ 绕过保护规则

开发者 (Developer):
  ✅ 创建 PR
  ✅ 审查他人代码
  ❌ 直接推送到主分支
  ❌ 绕过状态检查

访客 (Guest):
  ✅ 查看代码
  ✅ 创建 Issue
  ❌ 创建 PR
  ❌ 审查代码
```

### 5. 监控和审计

#### 保护规则变更记录

```bash
# GitHub 自动记录所有保护规则变更
Settings → Branches → Protection rules → View history

关注内容:
- 谁修改了规则？
- 什么时间修改？
- 修改了什么内容？
- 为什么修改？
```

#### 合规性检查

```bash
# 定期审查（建议每季度）
☑️ 保护规则是否与文档一致
☑️ 是否有未经授权的绕过
☑️ 状态检查是否正常工作
☑️ 审查人员配置是否合理
☑️ 权限分配是否合理
```

## 🚨 常见问题

### Q1: 为什么我不能推送到 main？

```
错误信息:
remote: error: GH006: Protected branch update failed
remote: error: Required status check "CI" is expected

原因:
✅ 分支保护规则生效
✅ 这是正常的，应该通过 PR 流程

解决方案:
1. 从 main 或 dev 创建功能分支
2. 在功能分支上开发
3. 推送功能分支
4. 创建 PR 到目标分支
5. 等待审查和状态检查通过
6. 合并 PR
```

### Q2: 如何处理 "required status check is failing"？

```
原因:
- CI 构建失败
- 测试未通过
- 代码检查不通过
- 分支不是最新的

解决方案:
# 1. 查看状态检查详情
在 PR 页面点击 "Details" 查看失败原因

# 2. 修复问题
git checkout feature/my-branch
# 修复代码
git add .
git commit -m "fix: resolve CI issues"
git push origin feature/my-branch

# 3. 如果是分支过期
git fetch origin
git rebase origin/dev
git push -f origin feature/my-branch
```

### Q3: PR 需要审查但团队只有我一个人？

```
方案 1: 调整审查人数要求为 0
Settings → Branches → Edit rule → Required approvals: 0

方案 2: 允许自己审查（不推荐）
Settings → Branches → Edit rule
☑️ Allow specified actors to bypass
添加自己的用户名

方案 3: 最佳实践
- 保持审查要求
- 自己先检查代码
- 过一段时间后自己批准
- 这样可以培养良好习惯
```

### Q4: 如何临时禁用分支保护？

```
不推荐！但如果必须:

1. Settings → Branches
2. 找到对应的保护规则
3. 点击 "Edit" 或 "Delete"
4. 临时禁用或删除规则
5. 完成操作后立即恢复

⚠️ 警告:
- 记录操作原因
- 尽快恢复保护
- 通知团队成员
```

### Q5: 合并后发现保护规则太严格，怎么办?

```
调整策略:

1. 评估当前规则
   - 哪些规则造成了阻碍？
   - 是否真的必要？
   - 团队反馈如何？

2. 逐步放宽
   # 从严到宽的顺序:
   - 减少必需的状态检查
   - 降低审查人数要求
   - 允许特定人员绕过
   - 完全移除保护

3. 文档化变更
   - 更新团队文档
   - 通知所有成员
   - 记录变更原因
```

### Q6: GitHub Actions 没有权限更新状态检查？

```
错误:
Resource not accessible by integration

解决方案:
1. Settings → Actions → General
2. Workflow permissions 设置为:
   ☑️ Read and write permissions
3. ☑️ Allow GitHub Actions to create and approve pull requests
4. Save 保存
```

## 📖 信息参考

### 官方文档

- [GitHub - About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [GitHub - Managing a branch protection rule](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule)
- [GitLab - Protected branches](https://docs.gitlab.com/ee/user/project/protected_branches.html)
- [Bitbucket - Branch permissions](https://support.atlassian.com/bitbucket-cloud/docs/use-branch-permissions/)

### DailyUse 项目相关文档

- [Git Flow 工作流规范](./GITFLOW.md) - 完整的分支管理流程
- [Git Flow 快速参考](./GITFLOW_QUICK_REFERENCE.md) - 常用命令速查
- [Git Flow 设置报告](../GITFLOW_SETUP_REPORT.md) - 项目设置记录

### 最佳实践参考

- [Atlassian - Branch permissions best practices](https://www.atlassian.com/git/tutorials/git-hooks#branch-permissions)
- [Google - Code Review Developer Guide](https://google.github.io/eng-practices/review/)
- [Microsoft - Pull Request Best Practices](https://docs.microsoft.com/en-us/azure/devops/repos/git/pull-requests)

### 工具和扩展

- [GitHub CLI](https://cli.github.com/) - 命令行管理 PR 和保护规则
- [Branch Protection Bot](https://github.com/apps/branch-protection-bot) - 自动化保护规则管理
- [Danger](https://danger.systems/) - PR 自动化检查工具
- [Probot](https://probot.github.io/) - GitHub App 框架，可自定义规则

### 安全相关

- [Git 签名提交指南](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)
- [GitHub 安全最佳实践](https://docs.github.com/en/code-security/getting-started/github-security-features)

## 🎯 快速配置清单

### 对于 DailyUse 项目

**立即执行**（5 分钟）:

```bash
# 1. 访问分支保护设置
https://github.com/BakerSean168/DailyUse/settings/branches

# 2. 为 main 分支添加保护规则
☑️ Require pull request before merging (1 approval)
☑️ Require status checks to pass before merging
☑️ Require conversation resolution before merging
☑️ Do not allow bypassing
❌ Allow force pushes
❌ Allow deletions

# 3. 为 dev 分支添加保护规则
☑️ Require pull request before merging (0 or 1 approval)
☑️ Require status checks to pass (if available)
❌ Allow force pushes
❌ Allow deletions

# 4. 保存并测试
```

**后续优化**（可选）:

```bash
# 1. 设置 GitHub Actions CI/CD
# 2. 配置 CODEOWNERS 文件
# 3. 添加更多状态检查
# 4. 设置签名提交
# 5. 配置自动化 PR 检查
```

---

## 💡 总结

分支保护是团队协作的**安全网**，而不是**障碍**。

**核心原则**:
1. ✅ 保护重要分支（main, dev）
2. ✅ 要求代码审查（至少 1 人）
3. ✅ 确保测试通过（CI/CD）
4. ✅ 禁止强制推送和删除
5. ✅ 根据团队规模调整策略

**记住**:
- 🔒 保护规则是为了保护团队，不是限制开发
- 📋 文档化你的保护策略
- 🔄 根据实际情况调整规则
- 👥 与团队沟通并达成共识

---

**文档版本**: 1.0  
**最后更新**: 2025-10-25  
**维护者**: @BakerSean168  
**适用项目**: DailyUse

