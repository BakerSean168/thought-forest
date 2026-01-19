---
tags:
  - type/howto
  - status/evergreen
  - tech/dev/library
description: 本文主要介绍如何使用 bmad-method 进行棕地项目开发
tool: bmad-method
created: 2025-12-16T14:56:23
updated: 2025-12-16T14:57:55
---

# 棕地项目开发（bmad-method） 

bmad-method 应该是在 高质量的项目 中继续 高质量地开发。

所以第一步就是要生成高质量的文档。

## 棕地项目流程总结

根据下面的标准流程图 + [[bmad-method agents]] 的每个阶段需要的 对应的 agent 和工作流：

文档化：
使用 `@analyst` 的 `*document-project` 给项目生成（优化）文档

规划：
1. 使用 `@pm` 的 `*create-prd` 生成 PRD 文档
2. 使用 `@ux-designer` 的 `*create-ux-design` 生成 UX 方案（可选）
3. 使用 `@architect` 的 `*create-architecture` 生成适应规模的架构方案
4. 没找到对应的agent 的功能  验证 archiecture（可选）
5. 使用 `@pm` 的 `*create-epics-and-stories` 将 PRD 拆解为可实施的片段（**必须在架构设计之后运行**）
6. 使用 `@sm` 的 `*sprint-planning` 初始化 `sprint-status.yaml` 跟踪文件。
7. 使用 `@sm` 的 `*create-story` 创建 故事

## 文档化（Documentation: Critical First Step）

bmad-method 提供了一个 文档化 的工作流

使用  `analyst` agent 的 `*document-project` 工作流

文档要求：
- ✅ `docs/index.md` exists and is comprehensive
- ✅ Documentation updated within last 30 days
- ✅ All doc files <500 lines with clear structure
- ✅ Covers architecture, patterns, setup, API surface
- ✅ You personally verified quality for AI consumption
- ✅ Previous AI agents used it successfully

## 选择开发工作流（Workflow Phases by Track）

对于不同规模大小的任务需要不同的工作流， bmad 提供了三种 **track**（见 moc 的概念部分）

![[BMAD-Method MOC#动态赛道 (Adaptive Tracks)]]

### 第一阶段：分析（可选）

[官方文档对分析阶段的详细介绍](https://github.com/bmad-code-org/BMAD-METHOD/blob/main/src/modules/bmm/docs/workflows-analysis.md)

第一阶段（分析）工作流程是**可选的**探索和发现工具，有助于在规划开始之前验证想法、了解市场并生成战略背景。

**何时使用：** 启动新项目、探索机会、验证市场契合度、产生想法、了解问题领域。
比如在 dailyuse 项目的 goal 模块中，先分析 ork 目标模块**需要哪些功能**、**是否需要xx功能**

**何时可以跳过：** 继续现有项目，这些项目具有明确的需求、明确的功能、已知的解决方案、严格的约束，并且探索已经完成。
已经明确需要哪些功能


### ### 第二阶段：规划（必需）

**Planning Docs**（需要下面的规划文件）:
- PRD.md (functional and non-functional requirements)
- Architecture.md (system design)
- UX Design (if UI components)
- Epics and Stories (created after architecture)

**Workflow Path**:

```
(Brownfield: document-project first if needed)
↓
(Optional: Analysis phase - brainstorm, research, product brief)
↓
PRD → (Optional UX) → Architecture → Create Epics and Stories → Implementation Readiness Check → Implement
```

### 第三阶段：解决方案制定

**Critical for brownfield（对棕地开发至关重要）：**
- Review existing architecture FIRST  
    首先审查现有架构
- Document integration points explicitly  
    文档集成点明确
- Plan backward compatibility  
    计划向后兼容性
- Consider migration strategy  
    考虑迁移策略

**Workflows（工作流程）：**
- `create-architecture` - Extend architecture docs (BMad Method/Enterprise)  
    `create-architecture` - 扩展架构文档（BMad 方法/企业版）
- `create-epics-and-stories` - Create epics and stories AFTER architecture  
    `create-epics-and-stories` - 在架构之后创建史诗和故事
- `implementation-readiness` - Validate before implementation (BMad Method/Enterprise)  
    `implementation-readiness` ——实施前验证（BMad 方法/企业）

### 第四阶段：实施



## 参考

[BMAD-METHOD/src/modules/bmm/docs/brownfield-guide.md at main · bmad-code-org/BMAD-METHOD --- BMAD-METHOD/src/modules/bmm/docs/brownfield-guide.md at main · bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD/blob/main/src/modules/bmm/docs/brownfield-guide.md#common-scenarios)



![[Pasted image 20251216161314.png]]