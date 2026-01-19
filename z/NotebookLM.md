---
tags: [tech/app/tools, type/howto, status/seed]
description: Google NotebookLM 的详细使用指南，涵盖从创建笔记本到音频概览生成的全流程。
created: 2025-12-08T22:12:00
updated: 2025-12-08T22:26:19
---

> [!info] **上级索引**
> [[AI MOC]] | [[Resource]] | [[000_Home]]

---

# NotebookLM 使用指南

> Google NotebookLM 是一款基于 RAG (检索增强生成) 技术的 AI 笔记与研究助手。它允许用户上传私有文档作为"源"，并完全基于这些源进行回答、总结和创意生成，极大地降低了 AI 幻觉的风险。

---

## 1. 核心概念

在使用 NotebookLM 之前，理解以下三个概念有助于更好地掌握工作流：

* **Notebook (笔记本)**：这是你的项目容器。每个笔记本是独立的，拥有独立的上下文环境。
* **Source (源)**：你上传的资料（PDF, Google Docs, 网页链接, 粘贴的文本等）。AI 的回答将严格限制在这些源的范围内。
* **Audio Overview (音频概览)**：NotebookLM 的特色功能，能够将你的源文档转化为一段类真人的双人播客对话。

## 2. 快速开始步骤

### Step 1: 创建笔记本

1. 访问 [NotebookLM 官网](https://notebooklm.google.com/)。
2. 点击页面左上角的 **"New Notebook"** 方块。
3. 为你的笔记本命名（例如："2025 行业分析报告"）。

### Step 2: 添加源 (Sources)

进入笔记本后，你需要通过左侧边栏添加资料。支持以下格式：
* **本地文件**：PDF, TXT, Markdown, Audio (MP3/WAV)。
* **Google Drive**：Docs, Slides, PDF。
* **Web**：网站链接 (URL)。
* **文本**：直接粘贴剪贴板内容。

> [!tip] 提示
> 目前每个笔记本最多支持上传 50 个源，每个源最多 500,000 字。

*目前不支持文件夹导入，可以在资源管理器中筛选 `*.md` 就能快速选出所有文件夹下的笔记*

### Step 3: 交互与查询

上传完成后，NotebookLM 会自动生成一份简短的摘要和建议问题。
* **提问**：在底部的聊天框输入问题（例如："总结文档中提到的三个主要风险"）。
* **引用检查**：AI 的回答会带有数字角标（如 `[1]`），点击可直接跳转到源文档对应的段落，方便核实信息的准确性。

## 3. 进阶功能：音频概览 (Audio Overview)

这是 NotebookLM 最具病毒传播效应的功能，适合通勤或做家务时"听"资料。

1. 点击界面右上角的 **"Notebook Guide"**（如果未自动弹出）。
2. 在 "Audio Overview" 区域，点击 **"Generate"**。
3. 等待几分钟，系统将生成一段 5-15 分钟的英文对话（目前主要支持英文语境，但可以解读中文文档）。
4. **自定义指令**：在生成前，你可以点击 "Customize" 按钮，告诉 AI 关注特定的主题或调整对话的语气。

## 4. 最佳实践场景

* **学术论文阅读**：上传多篇 PDF，询问它们之间的共同点和分歧点。
* **会议记录整理**：上传会议录音或转录稿，要求生成行动清单 (Action Items)。
* **学习新技能**：上传教程文档，让 AI 生成测验题或学习大纲。

---

## 🔗 相关链接

- [[AI 辅助学习工作流]] - 结合 Obsidian 与 AI 的学习方法
- [[RAG 技术原理解析]] - 了解 NotebookLM 此时的技术逻辑

---

## 参考资料

- [Google NotebookLM 官方网站](https://notebooklm.google.com/)
- [NotebookLM 官方帮助文档](https://support.google.com/notebooklm)
