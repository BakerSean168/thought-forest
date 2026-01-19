---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian 写作与内容创作完整工作流 - 结合 Templater + Linter + Periodic Notes
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[Obsidian插件完全索引]]

---

# Obsidian 写作工作流完整指南

> **目标**：建立高效的写作、编辑、发布工作流  
> **涉及插件**：Templater + Linter + Periodic Notes + Excalidraw + Better Export PDF  
> **难度级别**：⭐⭐⭐  
> **推荐度**：⭐⭐⭐⭐⭐  
> **适用人群**：博主、作家、内容创作者、记者、学生

---

## 📋 快速导航

- [工作流概览](#工作流概览)
- [核心工具](#核心工具)
- [日记与周期笔记](#日记与周期笔记)
- [内容创作流程](#内容创作流程)
- [编辑与发布](#编辑与发布)
- [进阶技巧](#进阶技巧)
- [完整示例](#完整示例)

---

## 工作流概览

### 内容创作全流程

```
创意激发
    ↓
[[灵感库]] (快速记录)
    ↓
[[日记]] (每日思考)
    ↓
选题与规划
    ↓
[[文章模板]] (Templater 生成)
    ↓
初稿写作
    ↓
[[Linter]] (自动格式化)
    ↓
自我编辑
    ↓
[[Excalidraw]] (添加图片/图表)
    ↓
最终校对
    ↓
[[Better Export PDF]] (导出)
    ↓
发布到网站/平台
    ↓
[[总结]] (记录收获)
```

---

## 核心工具

### 1️⃣ Templater（自动化模板）

**功能**：基于 JavaScript 的高级模板引擎

**三层模板**

```
1. 文章模板（Article Template）
   ├─ 标准文章结构
   ├─ SEO 元数据
   └─ 发布清单

2. 日记模板（Daily Template）
   ├─ 日期和时间戳
   ├─ 每日问题
   └─ 回顾总结

3. 周记模板（Weekly Template）
   ├─ 周学习总结
   ├─ 优秀内容链接
   └─ 下周计划
```

**模板示例**

`templates/article.md`：

```markdown
---
title: "<% tp.file.title %>-编辑中"
author: "Your Name"
date: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
category: ""
tags:
  - 
status: "draft"
word_count: 0
reading_time: "0 min"
---

# <% tp.file.title %>

> 一句话总结本文内容

## 导读

- 本文讨论的主要问题
- 读者能学到什么
- 阅读建议

---

## 背景

---

## 主体内容

### 第一部分

### 第二部分

### 第三部分

---

## 总结与建议

---

## 延伸阅读

- [[相关笔记1]]
- [[相关笔记2]]

---

## 编辑清单

- [ ] 检查语法错误
- [ ] 验证链接
- [ ] 添加目录
- [ ] 检查图片
- [ ] SEO 检查
- [ ] 最终校对

---

**发布日期**: 待定
**访问链接**: 待发布
```

**使用方法**：

```
命令面板 → Templater: Create new note from template
→ 选择 article.md
→ 自动生成包含当前日期的新笔记
```

### 2️⃣ Linter（自动格式化）

**功能**：自动规范化 Markdown 和 YAML 格式

**推荐规则**

```yaml
# 启用的规则
Rules:
  Heading Blank Lines:
    enabled: true
    bottom: 1       # 标题后 1 空行
    
  List Consistent Markers:
    enabled: true
    
  No Trailing Punctuation in Heading:
    enabled: true
    
  Trailing Spaces:
    enabled: true
    
  Two Spaces Between Lines:
    enabled: true
    
  YAML Key Sort:
    enabled: false  # 保持原有顺序
    
  Paste as Plain Text:
    enabled: true
    
  Lowercase Tags:
    enabled: false  # 保留大小写
```

**自动应用**：

```
Settings → Linter
→ Lint on Save: ✅
→ Display Lint Errors: ✅
```

每次保存都自动格式化。

### 3️⃣ Periodic Notes（日记管理）

**功能**：自动创建每日/每周/每月笔记

**日记模板** `daily-template.md`：

```markdown
# <% tp.date.now("YYYY-MM-DD dddd") %>

> 每日反思与总结

## 📅 今日计划

- [ ] 任务 1
- [ ] 任务 2
- [ ] 任务 3

## 📝 今日日志

### 上午

### 下午

### 晚上

## 💡 今日灵感

- 想法 1
- 想法 2

## 📊 今日数据

- 完成任务: ? / 3
- 学习时间: ? 小时
- 阅读时间: ? 分钟

## 🎯 明日重点

- 

## 📌 反思

> 今天最大的收获是什么？

---

**标签**: #daily #<% tp.date.now("YYYY-MM") %>
```

**周记模板** `weekly-template.md`：

```markdown
# 第 <% tp.date.now("ww") %> 周总结 (<% tp.date.now("YYYY-MM-DD") %>)

## 📚 本周学习

### 新学内容
- [[笔记1]]
- [[笔记2]]

### 知识点总结

## 📝 本周写作

- [[文章1]]
- [[文章2]]

完成 ? 篇文章

## 🎯 完成度

| 目标 | 计划 | 完成 | 进度 |
|-----|------|------|------|
| 写文章 | 3 篇 | ? | ?% |
| 学习 | 10h | ? | ?% |
| 阅读 | 5 篇 | ? | ?% |

## 🏆 周成就

- 

## 🔍 下周计划

- 

## 💭 反思总结

> 本周最重要的学习是什么？

---

**标签**: #weekly #<% tp.date.now("YYYY-MM") %>
```

**创建周期笔记**

在 Obsidian 中打开命令面板：

```
Periodic Notes: Open today's note
Periodic Notes: Open this week's note
Periodic Notes: Open this month's note
```

---

## 日记与周期笔记

### 日记工作流

**早上**（5 分钟）

```
1. 打开今日笔记
   命令: Periodic Notes: Open today's note
   
2. 填写每日计划（用 Templater 预填）

3. 记录今日重点目标
```

**工作中**（随时）

```
1. 有新想法就记录
   - 灵感笔记
   - 学习笔记
   - 问题记录

2. 在日记中链接相关笔记
   [[灵感1]] - 解决方案思路
```

**晚上**（15 分钟）

```
1. 统计今日完成情况
2. 记录主要收获
3. 反思与总结
4. 规划明日任务
```

### 周记工作流

**周末**（30 分钟）

```
1. 打开周笔记
   命令: Periodic Notes: Open this week's note

2. 汇总本周学习内容
   - 链接本周创建的所有笔记
   - 提取关键知识点

3. 统计完成度
   - 写了几篇文章
   - 学了多少新内容
   - 读了多少资料

4. 写周反思
   - 本周最大收获
   - 遇到的问题与解决方案
   - 下周改进方向
```

### 月度笔记

**月末**（1 小时）

```markdown
# 2025年12月总结

## 📊 数据统计

| 指标 | 数值 |
|-----|------|
| 发布文章 | ? 篇 |
| 完成阅读 | ? 本 |
| 学习时间 | ? 小时 |
| 日记天数 | ? / 31 |

## 🎯 目标完成情况

- [ ] 目标1: ? / ?
- [ ] 目标2: ? / ?
- [ ] 目标3: ? / ?

## 🏆 本月亮点

- 完成的优秀内容
- 突破性进展
- 值得总结的经验

## 📈 趋势分析

[[month-2025-11]]（上月）vs 本月
- 产出增长: ?%
- 质量提升: ?
- 效率改进: ?

## 💡 下月计划

-
```

---

## 内容创作流程

### 创意激发与收集

**灵感来源**

```
1. 日常阅读
   → 有想法立即记录到日记
   
2. 交流讨论
   → 他人观点记录
   
3. 问题遇到
   → 解决过程记录
   
4. 思维火花
   → 及时捕捉想法
```

**灵感笔记模板**

```markdown
---
title: "灵感: {{title}}"
type: "inspiration"
status: "idea"
date_created: {{date}}
---

# {{title}}

## 核心想法

## 为什么重要

## 可能的表现形式

- [ ] 文章
- [ ] 教程
- [ ] 案例研究
- [ ] 工具分享

## 相关链接

## 完成状态

- [ ] 转化为文章
- [ ] 发布
- [ ] 总结反思
```

### 选题与规划

**内容日历**

```markdown
# 2025年12月写作计划

| 日期 | 标题 | 类别 | 状态 |
|------|------|------|------|
| 12-08 | JavaScript 新特性 | Tech | ✅ 发布 |
| 12-10 | 读书笔记: XXX | Reading | 📝 草稿 |
| 12-12 | 月度总结 | Life | 🗂️ 计划 |
| 12-14 | Docker 入门 | Tutorial | 📚 研究中 |

## 选题规则

- 技术类: 学以致用
- 读书类: 每周一篇
- 生活类: 月度总结
- 教程类: 深度内容
```

### 初稿写作

**写作环境优化**

```
Settings → Appearance
→ Focus Mode: ✅      (聚焦模式)
→ Typewriter Mode: ✅ (打字机模式)
→ Readable line length: 60-70  (行宽)
→ Font size: 16-18px           (字体大小)
```

**写作习惯**

```
1. 快速写作，不要编辑
   - 先完成初稿（50% 的想法）
   - 再进行编辑（完善表达）

2. 使用大纲指导
   - H1: 文章标题
   - H2: 主要章节
   - H3: 小节主题
   
3. 内部链接
   - 自然地添加相关笔记链接
   - 不用过度优化

4. 代码与例子
   - 包含真实示例
   - 提供可运行代码
```

---

## 编辑与发布

### 自我编辑流程

**第一稿 → 第二稿**（编辑阶段）

```
✅ 内容完整性
   - 核心要点都包括了吗？
   - 逻辑清楚吗？
   - 有遗漏或过度解释吗？

✅ 标题与小标题
   - 标题吸引人吗？
   - 小标题概括了内容吗？
   - 包含关键词了吗？

✅ 段落与句子
   - 段落长度合适吗？
   - 句子清楚吗？
   - 有啰嗦的地方吗？

✅ 例子与证据
   - 有具体例子吗？
   - 数据/引用支持吗？
   - 代码示例有效吗？

✅ 可读性
   - 字体/字号合适吗？
   - 有使用列表/表格/代码块吗？
   - 有足够的空白吗？
```

**编辑检查清单**（使用 Task 插件）

```markdown
## 编辑清单

### 内容编辑
- [ ] 初稿完成
- [ ] 内容审查（逻辑清晰）
- [ ] 删除冗余信息
- [ ] 补充缺失信息
- [ ] 重新组织结构

### 文字编辑
- [ ] 修正拼写/语法
- [ ] 统一术语
- [ ] 改进词汇选择
- [ ] 优化句子表达
- [ ] 检查标点符号

### 格式编辑
- [ ] [[Linter]] 自动格式化
- [ ] 检查 Markdown 语法
- [ ] 检查标题级别
- [ ] 优化代码块
- [ ] 验证链接

### 多媒体
- [ ] 添加图片
- [ ] [[Excalidraw]] 绘制图表
- [ ] 优化图片尺寸
- [ ] 添加图片说明
- [ ] 检查图片质量

### 最终审核
- [ ] SEO 优化（标题、描述、关键词）
- [ ] 公开链接测试
- [ ] 代码可运行性测试
- [ ] 移动设备预览
- [ ] 最终校对
```

### 添加多媒体

**使用 Excalidraw**

```
1. 命令: Excalidraw: Create new drawing
2. 绘制流程图、架构图、插图
3. 导出为 PNG，嵌入文章
   ![diagram](drawing.excalidraw.md)
```

**插入图片**

```markdown
![描述性文字](image.png)

或使用 obsidian 图片语法：
![[image.png]]
```

### 导出与发布

**导出为 PDF**（使用 Better Export PDF）

```
命令: Better Export PDF: Export as PDF
→ 选择配置
→ 导出成功

或快捷键: Ctrl+P → search "Export PDF"
```

**导出为 Word/Google Docs**

```
1. 导出为 Markdown
2. 在线转换工具
   Pandoc: https://pandoc.org/
   CloudConvert: https://cloudconvert.com/
```

**发布到网站**

```
方式1: Obsidian Git + Quartz
  → 自动发布到博客

方式2: Digital Garden
  → 一键发布

方式3: 手动复制
  → 复制 Markdown 到博客平台
```

---

## 进阶技巧

### 1. 建立文章库系统

**按类别组织**

```
Articles/
├── Tech/
│   ├── JavaScript/
│   │   ├── [[closure.md]]
│   │   ├── [[async-await.md]]
│   │   └── ...
│   ├── Python/
│   └── ...
├── Reading/
│   ├── [[book-review-1.md]]
│   └── ...
├── Life/
│   └── [[monthly-summary.md]]
└── Templates/
    ├── article.md
    ├── daily.md
    └── weekly.md
```

**使用 MOC 索引**

```markdown
# 技术文章索引

## JavaScript
- [[闭包详解]]
- [[异步编程]]
- [[this 绑定]]

## Python
- [[装饰器]]
- [[上下文管理器]]

## 最新发布
- [[2025-12-08|新文章]]

## 按热度排序
按访问量/评论数统计
```

### 2. 编写可维护的文章

**标准化元数据**

```yaml
---
title: "文章标题"
author: "作者名"
date: 2025-12-08
updated: 2025-12-08
category: "技术"
tags:
  - javascript
  - web-dev
  - tutorial
status: "published"  # draft/published/archived
word_count: 2500
reading_time: "10 min"
difficulty: "中等"   # 初级/中等/高级
seo:
  description: "简短描述（160 字以内）"
  keywords: "关键词1, 关键词2"
featured_image: "cover.jpg"
---
```

**文章内部链接**

```markdown
## 相关文章

- [[前置知识|Prerequisites]]
- [[进阶内容|Advanced Topics]]
- [[完整代码仓库|Repository]]

## 参考资源

- [官方文档](https://...)
- [[本地笔记|Local Note]]
```

### 3. 内容版本管理

**使用 Git 追踪历史**

```bash
# 查看文章的修改历史
git log --oneline -- articles/article-name.md

# 查看具体修改
git show commit-hash:articles/article-name.md
```

**在 frontmatter 记录版本**

```yaml
---
versions:
  - v1.0: 初始发布 (2025-12-08)
  - v1.1: 修正示例代码 (2025-12-09)
  - v1.2: 添加更多案例 (2025-12-10)
---
```

### 4. 性能优化

**优化加载速度**

```markdown
## 图片优化

1. 压缩图片
   工具: Tinify, ImageOptim
   
2. 使用 WebP 格式
   ![image](image.webp)
   
3. 懒加载
   ![image](image.jpg){loading=lazy}
```

**优化 Markdown**

```
❌ 使用过多插件
✅ 保持 Markdown 简洁
❌ 非必要的嵌套
✅ 扁平化结构
```

---

## 完整示例

### 日常写作流程（一篇文章完整过程）

**Day 1: 想法与选题**

```
8:00 早读中产生想法
  → 快速记录到日记

12:00 整理想法
  → 创建灵感笔记 "JavaScript Promise 解析"

18:00 评估选题
  → 决定转化为文章
  → 添加到内容日历
```

**Day 2: 研究与规划**

```
9:00 开始研究
  → 链接相关笔记
  → 记录关键信息

14:00 制定大纲
  → 使用 H2/H3 列出结构
  → 添加代码示例位置
```

**Day 3: 初稿写作**

```
10:00 快速写作初稿
  → 关闭编辑,只写不改
  → 插入代码示例
  → 添加内部链接

16:00 初稿完成
  → 第一次阅读
  → 记录改进点
```

**Day 4: 编辑与美化**

```
9:00 自我编辑
  → 修正语法与表达
  → 删除冗余内容
  → 补充缺失信息

11:00 [[Linter]] 格式化
  → 自动规范 Markdown

13:00 添加多媒体
  → 使用 [[Excalidraw]] 绘制流程图
  → 优化代码示例

15:00 最终审核
  → 检查链接
  → 验证代码可运行
  → 移动设备预览
```

**Day 5: 发布与总结**

```
10:00 导出 PDF
  → 使用 Better Export PDF

11:00 发布文章
  → 推送到 GitHub (Quartz)
  → 发布完成

16:00 记录反思
  → 写发布日记
  → 链接到发布的文章
  → 记录反馈来源
```

---

## 📚 相关文档

- [[Templater插件详细指南]] - 高级模板编写
- [[Linter插件详细指南]] - 格式化规则详解
- [[Periodic Notes插件详细指南]] - 日记设置
- [[Excalidraw插件详细指南]] - 白板画图
- [[Better Export PDF插件详细指南]] - PDF 导出

---

## 🎯 写作建议

1. **保持一致的发布节奏**
   - 每周 1-2 篇（可持续）
   - 定时发布（固定读者预期）

2. **质量优先**
   - 一篇 2000+ 字的深度文章 > 十篇 200 字的短文
   - 可运行的代码示例很重要

3. **建立反馈机制**
   - 添加评论系统
   - 关注社区反应
   - 定期更新文章

4. **长期积累**
   - 不要删除文章
   - 定期更新旧文章
   - 建立内部链接网络

5. **坚持原创**
   - 加入自己的思考和观点
   - 提供真实案例
   - 分享学习过程

---

**上级索引**：[[Obsidian插件完全索引]] | [[Obsidian MOC]]
