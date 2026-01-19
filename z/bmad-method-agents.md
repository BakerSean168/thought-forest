---
tags:
  - status/growing
  - type/concept
description: BMad 方法模块 (BMM) 提供了一支由专业人工智能代理组成的综合团队，可指导您完成整个软件开发生命周期。每个代理都扮演着特定的角色，拥有独特的专业知识、沟通风格和决策原则。
tool: bmad-method
created: 2025-12-16T16:59:58
updated: 2025-12-16T17:01:05
---

# bmad-method agents 

## 基础 agent

我感觉 bmad-method 中的 agents 更加类似流水线上的机器，他们并非有专业知识的专家，更像有特定功能按钮的机器人。
每个 agents 都有特定的 workflows， 功能、职责。

可以借此了解一个各个岗位的职能。

比如棕地项目需要各个 ai 的特定 workflow，按顺序执行


## 参考

[官方的 agent 文档](https://github.com/bmad-code-org/BMAD-METHOD/blob/main/src/modules/bmm/docs/agents-guide.md)

这份指南详细整理了 BMad Method Module (BMM) 中所有智能体（Agent）的角色、职责、工作流及使用场景。

> **注意**：本文档已根据您的要求移除所有表格，并将信息重组为清晰的列表形式。

-----

# BMad Method 智能体完全指南

**阅读时间：** 约 45 分钟

## 目录

1.  [概览](https://www.google.com/search?q=%23%E6%A6%82%E8%A7%88)
2.  [核心开发智能体 (Core Agents)](https://www.google.com/search?q=%23%E6%A0%B8%E5%BF%83%E5%BC%80%E5%8F%91%E6%99%BA%E8%83%BD%E4%BD%93)
3.  [游戏开发智能体 (Game Agents)](https://www.google.com/search?q=%23%E6%B8%B8%E6%88%8F%E5%BC%80%E5%8F%91%E6%99%BA%E8%83%BD%E4%BD%93)
4.  [特殊用途智能体 (Special Agents)](https://www.google.com/search?q=%23%E7%89%B9%E6%AE%8A%E7%94%A8%E9%80%94%E6%99%BA%E8%83%BD%E4%BD%93)
5.  [Party Mode：多智能体协作](https://www.google.com/search?q=%23party-mode)
6.  [通用工作流与验证](https://www.google.com/search?q=%23%E9%80%9A%E7%94%A8%E5%B7%A5%E4%BD%9C%E6%B5%81%E4%B8%8E%E9%AA%8C%E8%AF%81)
7.  [智能体自定义](https://www.google.com/search?q=%23%E6%99%BA%E8%83%BD%E4%BD%93%E8%87%AA%E5%AE%9A%E4%B9%89)

-----

## 概览

BMad Method Module (BMM) 提供了一支由 13 位专业 AI 智能体组成的团队，覆盖软件开发的完整生命周期。

  * **核心开发 (9位):** PM, Analyst, Architect, SM, DEV, TEA, UX Designer, Technical Writer, Principal Engineer
  * **游戏开发 (3位):** Game Designer, Game Developer, Game Architect
  * **元管理 (1位):** BMad Master

-----

## 核心开发智能体

### 1\. PM (Product Manager) - John 📋

**角色：** 调查型产品策略师 + 市场敏锐的 PM

**适用场景：**

  * 为 L2-4 级项目创建产品需求文档 (PRD)。
  * 为小型项目 (L0-1 级) 创建技术规格说明书。
  * 在架构设计完成后，将需求拆解为史诗 (Epics) 和故事 (Stories)。
  * 验证规划文档。
  * 在实施过程中进行路线修正。

**主要阶段：** 第 2 阶段 (Planning - 规划)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `create-prd`：为 L2-4 项目创建 PRD（仅生成功能/非功能需求）。
  * `tech-spec`：为 L0-1 项目创建快速规格说明。
  * `create-epics-and-stories`：将 PRD 拆解为可实施的片段（**必须在架构设计之后运行**）。
  * `implementation-readiness`：验证 PRD + 架构 + Epics + UX 的完整性（可选）。
  * `correct-course`：处理项目实施中途的变更。
  * `workflow-init`：初始化工作流跟踪。

**风格与专长：** 直接、分析型。擅长挖掘根本原因，利用数据支持建议。专注于市场研究、MVP 优先级排序和适应不同规模的规划。

-----

### 2\. Analyst (Business Analyst) - Mary 📊

**角色：** 战略业务分析师 + 需求专家

**适用场景：**

  * 项目头脑风暴和构思。
  * 为战略规划创建产品简报。
  * 进行各类研究（市场、技术、竞品）。
  * 为既有项目（Brownfield）编写文档。

**主要阶段：** 第 1 阶段 (Analysis - 分析)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `brainstorm-project`：构思与方案探索。
  * `product-brief`：定义产品愿景与战略。
  * `research`：多类型研究系统。
  * `document-project`：既有代码库的综合文档编写。
  * `workflow-init`：初始化工作流跟踪。

**风格与专长：** 系统化、层级化。擅长需求获取、战略咨询及分析既有代码库。

-----

### 3\. Architect - Winston 🏗️

**角色：** 系统架构师 + 技术设计负责人

**适用场景：**

  * 为 L2-4 级项目设计系统架构。
  * 制定技术设计决策。
  * 验证架构文档。
  * 验证实施准备情况（从阶段 3 过渡到阶段 4）。
  * 实施过程中的技术路线修正。

**主要阶段：** 第 3 阶段 (Solutioning - 方案)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `create-architecture`：生成适应规模的架构方案。
  * `implementation-readiness`：验证 PRD + 架构 + Epics + UX 是否就绪。

**风格与专长：** 务实且全面。擅长分布式系统、云基础设施 (AWS/Azure/GCP)、API 设计、微服务及系统迁移策略。

-----

### 4\. SM (Scrum Master) - Bob 🏃

**角色：** 技术 Scrum Master + 故事准备专家

**适用场景：**

  * 初始化 Sprint 规划和跟踪。
  * 创建用户故事。
  * 组装动态故事上下文 (Context)。
  * 标记故事为“待开发 (ready for development)”状态。
  * Sprint 回顾。

**主要阶段：** 第 4 阶段 (Implementation - 实施)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `sprint-planning`：初始化 `sprint-status.yaml` 跟踪文件。
  * `create-story`：从 Epic 创建下一个故事（设置状态为 `ready-for-dev`）。
  * `validate-create-story`：可选的质量检查（不改变状态，建议在开发前运行）。
  * `epic-retrospective`：Epic 完成后的回顾。
  * `correct-course`：处理实施期间的变更。

**风格与专长：** 任务导向、高效。消除歧义，专注于清晰的任务交接和流程完整性。

-----

### 5\. DEV (Developer) - Amelia 💻

**角色：** 高级实施工程师

**适用场景：**

  * 实施带有测试的用户故事。
  * 对已完成的故事进行代码审查 (Code Review)。
  * 在满足“完成定义 (DoD)”后标记故事为完成。

**主要阶段：** 第 4 阶段 (Implementation - 实施)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `dev-story`：实施故事，包含：逐项任务迭代、测试驱动开发 (TDD)、多轮运行能力（初始开发+修复）、严格的文件边界强制。
  * `code-review`：高级开发人员级别的审查，包含：故事上下文感知、Epic 技术一致性检查、仓库文档引用。

**关键原则：**

  * **Story Context XML 是唯一的真理来源。**
  * 只有状态为 Approved 的故事才能开始开发。
  * 必须满足所有验收标准。
  * **完成前测试通过率必须达到 100%。**
  * 支持多次运行以修复审查后的问题。

-----

### 6\. TEA (Master Test Architect) - Murat 🧪

**角色：** 测试架构大师（带知识库）

**适用场景：**

  * 初始化测试框架。
  * ATDD 测试先行方法（在实施前）。
  * 测试自动化与覆盖率分析。
  * 设计综合测试场景。
  * CI/CD 流水线设置。
  * 非功能需求 (NFR) 评估。

**主要阶段：** 所有阶段 (Testing & QA)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `framework`：初始化生产级测试框架（智能选择 Playwright 或 Cypress）。
  * `atdd`：在实施前生成端到端 (E2E) 测试。
  * `automate`：全面的测试自动化。
  * `test-design`：基于风险的方法创建测试场景。
  * `trace`：需求到测试的溯源映射（质量门控）。
  * `nfr-assess`：验证非功能性需求。
  * `ci`：搭建 CI/CD 质量流水线。
  * `test-review`：利用知识库进行质量审查。

**风格与专长：** 数据驱动、注重实效。原则是“基于风险的测试”、“测试是功能开发的一部分，不是开销”。拥有专门的测试知识库访问权限。

-----

### 7\. UX Designer - Sally 🎨

**角色：** 用户体验设计师 + UI 专家

**适用场景：**

  * 重 UX 的项目 (L2-4)。
  * 设计思维研讨会。
  * 创建用户规格说明和设计产物。
  * 验证 UX 设计。

**主要阶段：** 第 2 阶段 (Planning - 规划)

**工作流：**

  * `workflow-status`：检查下一步该做什么。
  * `create-ux-design`：主持设计思维研讨会，定义 UX 规格（包含视觉探索、AI 辅助设计工具如 v0/Lovable 的使用、无障碍考虑）。
  * `validate-design`：验证 UX 规格和设计产物。

**风格与专长：** 富有同理心，用户至上。擅长用户研究、交互设计模式、WCAG 合规性及设计系统。

-----

### 8\. Technical Writer - Paige 📚

**角色：** 技术文档专家 + 知识策展人

**适用场景：**

  * 既有项目文档编写（Brownfield 必备）。
  * API 文档、架构文档、用户指南。
  * 创建 Mermaid 图表。
  * 改进 README 文件。
  * 解释复杂技术概念。

**主要阶段：** 所有阶段 (文档支持)

**工作流：**

  * `document-project`：综合项目文档编写（支持三种扫描级别：快速、深度、详尽；支持断点续写）。

**动作 (Actions)：**

  * `generate-diagram`：创建 Mermaid 图表（架构、时序、流程、ER 等）。
  * `validate-doc`：检查文档标准。
  * `improve-readme`：优化 README。
  * `explain-concept`：技术概念解释。
  * `standards-guide`：显示文档标准参考。
  * *(待办功能: create-api-docs, create-architecture-docs, create-user-guide, audit-docs)*

**风格与专长：** 耐心的导师风格。严格遵守 CommonMark 和 Google 开发者文档风格指南。

-----

### 9\. Principal Engineer (Technical Leader) - Jordan Chen ⚡

**角色：** 首席工程师 + 技术领导者

**适用场景：**

  * **Quick Flow (快速流)** 开发。
  * 创建可立即实施的技术规格。
  * 生产级质量的快速原型制作。
  * 性能关键型功能开发。
  * 需要快速交付且不牺牲质量时。

**主要阶段：** 所有阶段 (Quick Flow 通道)

**工作流：**

  * `create-tech-spec`：编写可直接实施的技术规格。
  * `quick-dev`：根据规格或指令直接执行开发。
  * `code-review`：高级代码审查。
  * `party-mode`：与其他智能体协作解决问题。

**风格与专长：** 沟通像 Git 提交信息一样简洁。擅长分布式系统、性能优化、重构及“第一性原理”解决问题。他是 Quick Flow 独奏开发模式的核心。

-----

## 游戏开发智能体

### 10\. Game Designer - Samus Shepard 🎲

**角色：** 首席游戏设计师 + 创意愿景架构师

**适用场景：**

  * 游戏头脑风暴与构思。
  * 创建游戏简报 (Brief)。
  * 编写游戏设计文档 (GDD)。
  * 叙事设计。
  * 游戏市场研究。

**主要阶段：** 第 1-2 阶段 (Analysis & Planning - 游戏)

**工作流：**

  * `workflow-status`：检查状态。
  * `workflow-init`：初始化。
  * `brainstorm-game`：游戏专题构思。
  * `create-game-brief`：定义游戏愿景。
  * `create-gdd`：完整的游戏设计文档（支持 24+ 种游戏类型注入、玩法优先哲学）。
  * `narrative`：叙事设计文档。
  * `research`：游戏市场研究。

-----

### 11\. Game Developer - Link Freeman 🕹️

**角色：** 高级游戏开发者 + 技术实施专家

**适用场景：**

  * 实施游戏故事。
  * 游戏代码审查。
  * 游戏开发 Sprint 回顾。

**主要阶段：** 第 4 阶段 (Implementation - 游戏)

**工作流：**

  * `workflow-status`：检查状态。
  * `dev-story`：执行游戏故事开发任务和测试。
  * `code-review`：执行游戏代码审查。

**专长：** Unity, Unreal, Godot, Phaser, 物理系统, AI 寻路, 性能优化。

-----

### 12\. Game Architect - Cloud Dragonborn 🏛️

**角色：** 首席游戏系统架构师 + 技术总监

**适用场景：**

  * 游戏系统架构设计。
  * 游戏技术基础设计。
  * 验证实施准备情况。

**主要阶段：** 第 3 阶段 (Solutioning - 游戏)

**工作流：**

  * `workflow-status`：检查状态。
  * `create-architecture`：游戏系统架构。
  * `implementation-readiness`：验证阶段 3 到 4 的过渡。
  * `correct-course`：处理技术变更。

**专长：** 多人游戏架构 (专用服务器/P2P)、引擎架构、资产管线优化。

-----

## 特殊用途智能体

### 13\. BMad Master 🧙

**角色：** 执行总管、知识保管人、工作流编排者

**适用场景：**

  * 列出所有可用任务和工作流。
  * 主持多智能体 Party Mode 讨论。
  * 跨模块的元级编排。
  * 了解 BMad Core 能力。

**主要阶段：** Meta (所有阶段)

**工作流：**

  * `party-mode`：全员群聊模式（见下文）。

**动作：**

  * `list-tasks`：列出 `task-manifest.csv` 中的任务。
  * `list-workflows`：列出 `workflow-manifest.csv` 中的工作流。

**风格：** 以第三人称称呼自己 ("BMad Master recommends...")。系统化呈现信息，通过编号列表引导用户选择。

-----

## Party Mode: 多智能体协作

Party Mode 允许将已安装的智能体拉入同一个对话中，进行多角度讨论、回顾和协作决策。

**如何启动：**

  * 命令：`/bmad:core:workflows:party-mode`
  * 或者从任何智能体处输入：`*party-mode`

**工作机制：** BMad Master 根据上下文每条消息协调 2-3 个相关智能体发言。
**最佳用途：** 战略决策、创意头脑风暴、事后复盘、Sprint 回顾。

-----

## 通用工作流与验证

### 通用工作流 (Universal Workflows)

以下工作流对多个智能体可用：

  * **`workflow-status`** (所有智能体): 检查当前项目状态并获取下一步建议。
  * **`workflow-init`** (PM, Analyst, Game Designer): 初始化工作流跟踪文件。
  * **`correct-course`** (PM, Architect, SM, Game Architect): 实施阶段的变更管理。
  * **`document-project`** (Analyst, Technical Writer): 既有项目 (Brownfield) 文档编写。

### 验证动作 (Validation Actions)

用于阶段转换前的独立审查：

  * **`implementation-readiness`** (Architect): 验证 PRD + 架构 + Epics + UX 是否就绪。
  * **`validate-design`** (UX Designer): 验证 UX 规格和设计产物。
  * **`validate-create-story`** (SM): 验证生成的故事文件质量。

-----

## 智能体自定义

你可以通过配置文件自定义任何智能体的个性，而无需修改核心文件。

**位置：** `{project-root}/_bmad/_config/agents/`
**命名：** `{module}-{agent-name}.customize.yaml` (例如 `bmm-pm.customize.yaml`)

**自定义结构示例：**

```yaml
agent:
  persona:
    displayName: 'Alex' # 覆盖显示名称
    communicationStyle: 'Casual and friendly.' # 覆盖沟通风格
    principles: # 添加或替换原则
      - 'HIPAA compliance is non-negotiable'
```

**生效步骤：**

1.  创建/修改 `.customize.yaml` 文件。
2.  运行安装程序：`npx bmad-method install` (必须重新生成 manifest)。
3.  重新加载智能体。

-----

## 最佳实践摘要

1.  **迷路时：** 随时运行 `*workflow-status`，智能体会分析项目状态并推荐下一步。
2.  **新项目 (Greenfield) 典型路径：**
      * `workflow-init` -\> `create-prd` -\> `create-architecture` -\> `create-epics-and-stories` -\> `sprint-planning`。
3.  **既有项目 (Brownfield) 典型路径：**
      * 先运行 `document-project`，然后 `workflow-init`，后续同上。
4.  **开发循环：**
      * SM: `create-story` -\> DEV: `dev-story` -\> DEV: `code-review`。
5.  **测试策略：**
      * TEA: `framework` (早期) -\> `atdd` (开发前) -\> `automate` (开发后)。
6.  **遇到困难决策：** 使用 `*party-mode` 让智能体们一起讨论权衡。

-----
