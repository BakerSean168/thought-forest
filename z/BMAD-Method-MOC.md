---
tags: [tech/ai/ml, type/concept, status/growing, type/moc]
description: BMAD-Method 是一个用于规范 ai 开发工作流的工具库。
updated: 2025-12-15T16:42:54
created: 2025-10-20T18:02:35
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---

# BMAD-Method

## 概览

现在 AI 编程助手的主要的使用方法应该就是（对话 + 引用 + prompt、rule 等提示词规约文件）。如果修改内容只涉及少量文件，少量功能，都能够快速准确的实现。但是当任务较多时，容易出现一下问题：
- 代码风格不一致
- 代码未使用已有基础设置，创建了新的
- 使用 todo 占位，未实际实现
- 部分要求直接忽视

个人认为 bmad-method 应该是一个强大的 prompt 工具，用于约束 ai 的行为，避免上面的问题。

## 概念 (Concepts)

首先要了解 bmad-method 中的智能体（agents），不同的 agents 有不同的专长，适合干不同的工作（类似内置了“你是xxx”的prompt）。

## 经验总结

- 根据需求选择适合的 track（方案，参考 Scale-Adaptive System），每个 track 有不同的流程
- 每个 agents 有不同的 workflow（功能，用于满足 track 流程中的需求）
[[棕地项目开发（bmad-method）]]


## 个人笔记

我认为 bmad-method 的本质是在推动代码的质量（注释、文档、标准化）

[[bmad-method agents]]（每个代理介绍的中文翻译）

bmad-method 
## 信息参考

[bmad-code-org/BMAD-METHOD: Breakthrough Method for Agile Ai Driven Development](https://github.com/bmad-code-org/BMAD-METHOD)

[BMAD官方文档](https://docs.bmad-method.org/)