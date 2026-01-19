---
tags:
  - tech/lang/typescript
  - type/howto
  - status/growing
description: DailyUse
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]]

---


# DailyUse


我需要你结合所有文档，写出最关键的几个文档：
1. 先讲讲各个系统、工具，每个系统、工具分别用一篇文档
	1. 日志系统、web端响应系统、校验（validator）系统、事件总线系统、测试系统、initialize系统等
2. 再讲讲各个项目、库，大概讲讲，需要具体讲的则另起一篇文档
3. 还有一篇最关键的，详细讲讲 Goal  模块的前后端的整个流程，直接把 web、api、domain 中的 goal 模块代码文件都以文件树的形式展示，并注释每个文件的作用。然后展示每个文件的样例代码，表现代码规范、使用到了哪些工具、系统
4. 上面的文档用于让用户能清晰地了解项目地重要模块、工具及使用方法；还有指导 agent 其他模块的重构方案、代码规范
5. 我会通过 obsidian 的 链接，把文档链接起来，比如 initialize系统等放在 utils 库里面；对于已经有文档的内容，你就用 `[[文档名称]]` 的语法表示参考关联该文档

## 想法

让每一个页面都可配置（隐藏、显示、单独window）
然后单独 window 一个 快捷方式打开页面
可以给每个页面添加一个快捷键（alt+ 空格）用于快捷方式页面

每个页面就是一个 插件？

## 🎯 BMAD Desktop Epic 工作流

### 完整角色协作流程

---

### **阶段 1: Epic 规划**

#### 👤 **PM (产品经理)**
```
让 PM 细化 EPIC-002
- 确认业务优先级
- 定义 MVP 功能范围
- 输出: 完善的 Epic 文档 + 用户故事
```

#### 👤 **Architect (架构师)**
```
让 Architect 审查 Desktop 架构
- 验证 DI 集成方案
- 确认 IPC 通信设计
- 输出: ADR 文档 (如需架构决策)
```

---

### **阶段 2: Story 细化**

#### 👤 **PM**
```
让 PM 创建 Story 文档
- STORY-002: 主进程 DI 初始化
- STORY-003: 渲染进程 DI 集成
- STORY-004: Preload API 暴露
- STORY-005: Goal 模块 UI
- ...
输出: docs/sprint-artifacts/stories/STORY-00X-*.md
```

#### 👤 **Architect**
```
让 Architect 为每个 Story 提供技术方案
- 确认接口设计
- 确认依赖关系
- 输出: Story 中的技术实现章节
```

---

### **阶段 3: 开发执行**

#### 👤 **Dev (开发者)**

**Story 1-3 (基础架构):**
```
让 Dev 执行 STORY-002
- 创建 desktop-main.composition-root.ts
- 创建 SQLite 适配器
- 验收: 主进程 Container 注入成功

让 Dev 执行 STORY-003
- 更新 renderer/main.ts 调用 configureDesktopDependencies
- 创建 electron.d.ts 类型声明
- 验收: 渲染进程 DI 配置成功

让 Dev 执行 STORY-004
- 扩展 preload.ts 暴露所有 IPC 方法
- 创建主进程 IPC handlers
- 验收: Goal 模块端到端通信成功
```

**Story 4+ (功能模块):**
```
让 Dev 执行 STORY-005 (Goal UI)
让 Dev 执行 STORY-006 (Task UI)
让 Dev 执行 STORY-007 (Schedule UI)
...
```

---

### **阶段 4: 质量保障**

#### 👤 **Dev**
```
让 Dev 编写测试
- 单元测试: 主进程服务
- 组件测试: Vue 组件
- E2E 测试: 关键流程
```

#### 👤 **Architect**
```
让 Architect 代码审查
- 验证架构一致性
- 检查包复用率
- 输出: PR Review 通过
```

---

### **阶段 5: 发布**

#### 👤 **Dev**
```
让 Dev 打包测试
- Windows/macOS/Linux 构建
- 安装测试
- 输出: 可发布的安装包
```

---

## 📋 简化版命令清单

```
1. 让 PM 细化 EPIC-002，输出完整的 Story 列表

2. 让 Architect 审查架构，确认 DI/IPC 方案

3. 让 PM 创建 STORY-002/003/004 文档

4. 让 Dev 执行 STORY-002 (主进程 DI)

5. 让 Dev 执行 STORY-003 (渲染进程 DI)

6. 让 Dev 执行 STORY-004 (Preload API)

7. 让 Dev 执行 STORY-005 (Goal 模块)

8. 让 Dev 执行 STORY-006 (Task 模块)

9. ... (后续 Story)

10. 让 Architect 代码审查

11. 让 Dev 打包发布
```

---

## 🚀 立即开始

**第一步：**
```
让 PM 基于 EPIC-002 创建 STORY-002 (主进程 DI 初始化)
```

需要我现在切换到 PM 角色开始吗？

## PRD

dailyuse 软件是希望融入用户的生活流中，帮助用户管理在“目标实现”、“文档生成”、“知识回顾”、“自我提升”中形成良性循环，最终 “lead a better life”。

当前设计有以下模块：
- goal，目标的管理，以 OKR 形式（一个目标分为几个关键结果，关键结果有进度），
- task，任务的管理，goal 中 关键结果 的载体 或者 其他普通任务，支持循环任务。
- reminder，提醒任务的管理，专注于一天内的习惯提醒，如起床、三餐、站起来活动等的提醒。
- repository，资源仓库（知识笔记、图片、音视频的仓库），笔记中应该支持嵌入多种媒体。
- editor，笔记的编辑，和 repository 应该是紧密联系的。
- schedule，用户日程的继承集成、可视化、管理模块，应该是能将 goal、task、reminder 等模块的 用户日程统一，实现 视图展示、冲突检测 等功能
- notification， 基础模块， 用于实现多样化的通知，邮件、弹窗、音效，还有与对应事件的交互，比如点击弹窗中的查看详情跳转到目标信息页面。
- accout 与 authentication， 基础模块，用户管理与认证模块
- setting, 提供一些设置功能，基础的主题，或者 editor、repository 等模块的设置
- ai， 扩展功能，提供 ai 来优化已有工作流，如帮助用户根据 idea 快速生成一个完整的目标、并生成对应的任务；还有快速生成用户想了解的知识的笔记等。
