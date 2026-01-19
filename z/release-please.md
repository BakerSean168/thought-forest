---
tags:
  - status/growing
  - type/resource
  - tech/ops
description: 谷歌开源的工具 googleapis/release-please-action。它的核心理念是 "基于提交信息的版本管理" (Conventional Commits)。
created: 2025-12-18T11:02:46
updated: 2025-12-18T11:03:04
---

# release-please 

Release Please 是 Google 开发的一个 **“基于 Git 提交信息（Commit Messages）自动化发布流程”** 的工具。用于摆脱手动修改版本号、手动写 Changelog 的痛苦。

它的核心逻辑可以概括为：**你只要按照规范写 Commit，剩下的发布工作全自动完成。**

---

## 原理（怎么写提交语句）

Release Please 并不是魔法，它极其依赖 **Conventional Commits（约定式提交）**。它会像一个阅卷老师一样，逐行阅读你两次发布之间的所有 Commit Message。

它根据 **“关键词”** 来决定怎么升级版本：

| 提交类型 (Prefix)                 | 含义     | 版本号影响 (SemVer)        | 例子                    |
| ----------------------------- | ------ | --------------------- | --------------------- |
| **`fix:`**                    | 修复 Bug | **Patch** (0.0.**x**) | `fix: 修复了登录按钮点击无效的问题` |
| **`feat:`**                   | 新功能    | **Minor** (0.**x**.0) | `feat: 增加黑暗模式支持`      |
| **`perf:`**                   | 性能优化   | **Patch** (通常)        | `perf: 优化首页加载速度`      |
| **`chore:`**                  | 杂务     | **无** (不触发发布)         | `chore: 更新依赖库`        |
| `feat!:`,`fix!:`,`refactor!:` | 重大变更   | **Major** (**x**.0.0) |                       |


**重要：** 如果你的 Commit 是乱写的（比如 `update code`, `fixed bug`），Release Please 会忽略它们，认为没有任何变更，因此**不会**创建新版本。

### [线性 Git 提交历史（使用 squash-merge）](https://github.com/googleapis/release-please#linear-git-commit-history-use-squash-merge)

我们**强烈**建议您在合并拉取请求时使用 squash-merge。线性 Git 历史记录可以大大简化以下操作：

- 遵循历史记录 - 提交按合并日期排序，并且不会在拉取请求中混淆
- 查找并回滚错误——`git bisect`有助于追踪是哪个更改引入了错误。
- 控制发布变更日志——合并 PR 时，您可能会遇到一些提交信息，这些信息在 PR 范围内有意义，但在合并到主分支后就显得不合理。例如，您可能提交了 `git commit -v` 和 `git commit -v` `feat: introduce feature A`。`fix: some bugfix introduced in the first commit`实际上`fix`，由于主分支中从未出现过任何 bug，因此该提交与发布说明无关。
- 保持主分支的清洁——如果你使用类似红/绿开发（在提交 A 中创建一个失败的测试，然后在提交 B 中修复）并合并（或变基合并）的方法，那么你的主分支中就会出现测试不通过的情况。

### [如果我的 PR 包含多个修复或功能怎么办？](https://github.com/googleapis/release-please#what-if-my-pr-contains-multiple-fixes-or-features)

Release Please 允许您使用页脚在单个提交中表示多个更改：

```adblock
feat: adds v4 UUID to crypto

This adds support for v4 UUIDs to the library.

fix(utils): unicode no longer throws exception
  PiperOrigin-RevId: 345559154
  BREAKING-CHANGE: encode method no longer throws.
  Source-Link: googleapis/googleapis@5e0dcb2

feat(utils): update encode to support unicode
  PiperOrigin-RevId: 345559182
  Source-Link: googleapis/googleapis@e5eef86
```

上述提交信息将包含：

1. **为“向加密添加 v4 UUID”**功能添加条目。
2. **添加了“unicode不再抛出异常”**的修复条目，并注明这是一个重大更改。
3. **为“更新编码以支持 Unicode”**功能添加条目。

⚠️ **重要提示：**附加消息必须添加到提交的末尾。

### [如何更改版本号？](https://github.com/googleapis/release-please#how-do-i-change-the-version-number)


当提交到主分支的**提交内容**中包含`Release-As: x.x.x`不区分大小写）时，Release Please 将为指定的版本打开一个新的拉取请求。

**空提交示例：**

`git commit --allow-empty -m "chore: release 2.0.0" -m "Release-As: 2.0.0"`提交信息如下：

```adblock
chore: release 2.0.0

Release-As: 2.0.0
```

### [如何修改发行说明？](https://github.com/googleapis/release-please#how-can-i-fix-release-notes)

如果您已合并一个拉取请求，并希望修改用于生成该提交发布说明的提交消息，您可以编辑已合并拉取请求的正文，并添加如下部分：

```
BEGIN_COMMIT_OVERRIDE
feat: add ability to override merged commit message

fix: another message
chore: a third message
END_COMMIT_OVERRIDE
```

下次 Release Please 运行时，它将使用该覆盖部分作为提交消息，而不是合并后的提交消息。

⚠️ **重要提示：**此功能不适用于普通合并，因为 release-please 无法确定要将覆盖应用于哪个提交。[我们建议改用 squash-merge](https://github.com/googleapis/release-please#linear-git-commit-history-use-squash-merge)。

---

## 工作流程：它的一生（生命周期）

Release Please 的工作流是一个无限循环，主要分为两个阶段：**“囤积阶段”** 和 **“爆发阶段”**。

### 阶段一：囤积阶段（The Release PR）

这是你日常开发时发生的事情。

1. 你在 `main` 分支合并了一个代码，比如 `feat: 添加搜索功能`。
2. Release Please 运行，发现这是一个新功能。
3. 它**不会立刻发布**，而是会检查有没有一个名为 `release-please--branches--main` 的 **Pull Request (PR)**。
* **如果没有：** 它会创建一个新的 PR。
* **如果有：** 它会更新这个 PR。


4. **在这个 PR 里，它做了两件事：**
* 修改 `CHANGELOG.md`：把你的 `feat: 添加搜索功能` 写入待发布日志。
* 修改 `package.json`：把版本号从 `1.0.0` 预改为 `1.1.0`。



**此时，这个 PR 就是一个“待办的发布单”。** 你可以继续提交代码，Release Please 会不断更新这个 PR，把新的改动加进去。

### 阶段二：爆发阶段（The Release）

当你觉得“功能攒够了，该发布了”：

1. 你作为管理员，点击那个 Release PR 的 **"Merge" (合并)** 按钮。
2. Release Please 再次被触发。
3. 它发现**是它自己创建的 PR 被合并了**。
4. 它执行最终操作：
	* 在 GitHub 上创建一个 **Tag** (例如 `v1.1.0`)。
	* 在 GitHub 上创建一个 **Release**，把 Changelog 贴进去。

## 配置

### github Action 中使用

[Github文档](https://github.com/googleapis/release-please-action)

#### Token

需要 github token，权限如下：
```
permissions:
  contents: write
  issues: write
  pull-requests: write
```

also need to set "Allow GitHub Actions to create and approve pull requests" under repository Settings > Actions > General.

#### Release Types Supported

这个参数告诉 Release Please 怎么处理版本文件。

* `node`: 它会去找 `package.json`。
* `simple`: 它通常只维护一个 `version.txt` 或者只管 Tag 和 Changelog，不深度绑定特定语言的包管理文件。
选择对应的配置才能够自动修改 包的版本管理，比如自动修改 package.json 中的版本号

## 参考

[google-release-please github 文档]https://github.com/googleapis/release-please
[google-release-please-action github 文档](https://github.com/googleapis/release-please-action)
[[Github Flow(主干开发模式)]]