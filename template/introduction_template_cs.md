---
tags:
  - tech
  - type/resource
  - status/growing
description: introduction_template_cs
---
<%* 
// 初始化
let title = tp.file.title;
if (title.startsWith('Untitled')) {
title = await tp.system.prompt('Enter the title');
if (!title) return;
}
if (title == '') {
title = 'Untitled'
} else {
await tp.file.rename(title);
}
await tp.file.move('/z/' + title)
// 更新 updated
if (!tp.frontmatter.created) { tp.frontmatter.created = tp.file.creation_date("YYYY-MM-DDTHH:mm:ss"); } tp.frontmatter.updated = tp.date.now("YYYY-MM-DDTHH:mm:ss"); 
%>
# <% title %> 

## 1. 概览（Overview）

* **这是一个什么东西？**
* **它解决什么问题？适用场景是什么？**
* **一句话总结**

---

## 2. 关键概念（Core Concepts）

* 概念 A：说明
* 概念 B：说明
* 专有名词 / 缩写解释

---

## 3. 安装与环境准备（Installation / Setup）

* 系统要求
* 安装步骤（命令行/GUI）
* 环境变量 / 配置说明

---

## 4. 快速开始（Quick Start）

* 最小可运行示例
* 基本使用流程
* 示例代码 / 指令

---

## 5. 进阶使用（Advanced Usage）

* 配置项说明
* 可扩展点 / 插件机制
* 常见模式（patterns）

---

## 6. 目录结构（Project Structure）

```
project/
  ├── config/
  ├── src/
  ├── ...
```

* 每个目录的作用说明

---

## 7. 常见问题（FAQ）

* Q1: …
* Q2: …

---

## 8. 排错指南（Debug / Troubleshooting）

* 常见报错 & 解决方法
* 检查顺序

---

## 9. 最佳实践（Best Practices）

* 推荐的工作流
* 最常见的踩坑
* 性能/安全注意点

---

## 10. 资源与参考（Resources）

* 官方文档
* 视频/博客
* GitHub 示例

---

## 11. 个人笔记（Personal Notes）

* 使用心得
* 自定义小技巧
* 后续学习方向


