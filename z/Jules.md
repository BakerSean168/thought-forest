---
tags:
  - tech/dev
  - type/resource
  - status/seed
description: Google 推出的自主 AI 代码代理人 (Code Agent)，基于 Gemini 2.5 Pro 模型，旨在处理复杂的编程任务和自动化工作流。
created: 2025-12-08T22:35:00
updated: 2025-12-09T09:13:57
category: resource
---

> [!info] **上级索引**
> [[AI MOC]] | [[Resource]] | [[000_Home]]

---

# Google Jules

## 概览（Overview）

> Jules 是 Google 推出的一款 **AI Code Agent (代码智能体)**。与传统的代码补全工具（如 Copilot）不同，Jules 被设计为更具"代理权"（Agency），能够理解模糊的自然语言指令，规划多步任务，并跨文件、跨仓库执行复杂的代码修改与维护工作。

---

## 1. 核心特性

Jules 的核心能力构建在 **Gemini 2.5 Pro** 模型之上，具备超长的上下文窗口和强大的逻辑推理能力。

### 🤖 自主代理 (Agency)

* **任务规划**：Jules 不仅仅是补全下一行代码，它可以接收如"重构登录模块并添加 OAuth 支持"这样的指令，然后自行拆解步骤。
* **环境感知**：它能读取整个仓库的上下文，理解项目结构、依赖关系和编码规范。

### 🛠️ 工作流集成

* **GitHub 集成**：Jules 可以直接在 GitHub Issues 或 Pull Requests 中被召唤，自动审查代码、提出修改建议甚至直接提交代码修复。
* **CI/CD 联动**：当 CI 构建失败时，Jules 可以分析错误日志，定位问题代码并尝试修复。

## 2. 适用场景

* **遗留代码维护**：让 Jules 分析旧代码库，解释逻辑并进行现代化重构。
* **单元测试生成**：针对特定模块，让 Jules 自动生成覆盖率高的测试用例。
* **Bug 修复**：直接将 Bug 报告或错误堆栈发给 Jules，让其寻找根因。

## 3. 与其他工具的对比

| 特性         | Google Jules   | GitHub Copilot     | Cursor / Windsurf | Devin            |
|:------------ |:-------------- |:------------------ |:----------------- |:---------------- |
| **定位**     | 智能体 (Agent) | 辅助助手 (Copilot) | AI 编辑器         | 软件工程师智能体 |
| **主要交互** | 任务导向       | 补全导向           | 对话/补全         | 全流程接管       |
| **核心模型** | Gemini 2.5 Pro | GPT-4o / Claude    | Claude / Custom   | Custom           |

---

## 个人经验

就是直接链接 Github 仓库，然后在使用虚拟机获取仓库代码，然后进行代理修改
## 参考资料

- [Google AI Blog - Introducing Jules](https://blog.google/technology/ai/)
- [Google Cloud - AI Agents](https://cloud.google.com/agents)
