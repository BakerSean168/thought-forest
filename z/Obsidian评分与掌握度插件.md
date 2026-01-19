---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian 笔记评分与掌握度跟踪插件推荐
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian]] | [[Resource]]

---

# Obsidian 评分与掌握度插件指南

> 使用 Obsidian 插件为笔记打分，追踪自己的学习进度和知识掌握度

---

## 🏆 推荐插件

### 1️⃣ **Dataview** ⭐⭐⭐⭐⭐

**最强大的查询与显示插件**

#### 功能
- 基于 frontmatter 元数据查询
- 创建动态表格、列表、日历
- 实时统计和聚合
- 不需要编程基础，使用 DQL 查询语言

#### 安装
在 Community Plugins 搜索 `Dataview` 并启用

#### 使用示例

**1. 在笔记 frontmatter 中添加评分字段**：

```yaml
---
title: JavaScript 基础
rating: 4
status: learning
created: 2025-12-08
---
```

**2. 创建查询页面，显示所有笔记按评分排序**：

```dataview
TABLE rating, status, created
FROM ""
WHERE rating
SORT rating DESC
```

**3. 统计学习进度**：

```dataview
TABLE 
  (row.rating * 20) as "掌握度"
FROM ""
WHERE rating > 0
SORT rating DESC
```

**4. 显示掌握度不足的笔记**：

```dataview
LIST
FROM ""
WHERE rating < 3
SORT updated DESC
```

#### Dataview 完整示例

创建一个 `学习仪表板.md`：

```markdown
---
title: 学习仪表板
---

## 📊 掌握度统计

### ⭐⭐⭐⭐⭐ 已完全掌握
\`\`\`dataview
LIST
FROM ""
WHERE rating = 5
\`\`\`

### ⭐⭐⭐⭐ 基本掌握
\`\`\`dataview
LIST
FROM ""
WHERE rating = 4
\`\`\`

### ⭐⭐⭐ 理解中
\`\`\`dataview
LIST
FROM ""
WHERE rating = 3
\`\`\`

### ⭐⭐ 初步学习
\`\`\`dataview
LIST
FROM ""
WHERE rating = 2
\`\`\`

### ⭐ 计划学习
\`\`\`dataview
LIST
FROM ""
WHERE rating = 1
\`\`\`

## 📈 学习统计

\`\`\`dataview
TABLE
  rating as "评分",
  (length(rows)) as "数量"
FROM ""
WHERE rating
GROUP BY rating
SORT rating DESC
\`\`\`

## 🔄 最近更新

\`\`\`dataview
TABLE updated, rating
FROM ""
SORT updated DESC
LIMIT 10
\`\`\`
```

---

### 2️⃣ **Ratings** ⭐⭐⭐⭐

**更简洁的评分插件**

#### 功能
- 单击添加星级评分
- 支持 0-5 星评分
- 在编辑模式中显示评分界面
- 自动更新 YAML frontmatter

#### 安装
搜索 `Ratings` 在 Community Plugins 中

#### 使用方法

**1. 在笔记顶部设置评分**：

```yaml
---
title: Python 基础
rating: 4
---
```

**2. 使用插件 UI 快速修改**：
- 点击星星图标直接评分
- 支持鼠标悬停预览评分

#### 与 Dataview 结合

```dataview
TABLE rating
FROM ""
WHERE rating > 0
SORT rating DESC
```

---

### 3️⃣ **Progress Bar** ⭐⭐⭐⭐

**学习进度可视化**

#### 功能
- 在笔记中显示进度条
- 支持百分比显示
- 自动计算完成度

#### 使用语法

```markdown
\`\`\`progress-bar
value: 75
max: 100
\`\`\`

<!-- 简化语法 -->
\`\`\`progress-bar
75
\`\`\`
```

#### 示例

```markdown
## 学习进度

### HTML 掌握度：75%
\`\`\`progress-bar
75/100
\`\`\`

### CSS 掌握度：60%
\`\`\`progress-bar
60/100
\`\`\`

### JavaScript 掌握度：80%
\`\`\`progress-bar
80/100
\`\`\`
```

---

### 4️⃣ **Spaced Repetition** ⭐⭐⭐⭐

**间隔重复学习（SRS）**

#### 功能
- 基于艾宾浩斯遗忘曲线
- 自动安排复习时间
- 追踪学习进度

#### 使用方法

**1. 标记需要复习的笔记**：

```yaml
---
title: 重点知识
sr-due: 2025-12-15
sr-interval: 10
sr-ease: 2.5
---
```

**2. 打开间隔重复窗口**：
- 使用命令面板 → Open Spaced Repetition View
- 按照提示学习和复习

---

### 5️⃣ **Habits** ⭐⭐⭐⭐

**习惯跟踪**

#### 功能
- 记录每日学习习惯
- 显示连续学习天数
- 可视化热力图

#### 使用示例

```yaml
---
title: 学习日志
habits:
  - 每日 30 分钟编程
  - 阅读技术文章
  - 写学习笔记
---
```

---

## 📋 完整工作流示例

### 步骤 1: 设置笔记模板

创建 `_template/Learning Note.md`：

```markdown
---
title: {{ title }}
rating: 0
status: planning
tags: 
  - learning
  - {{ category }}
created: {{date}}
updated: {{date}}
---

# {{ title }}

## 学习目标
- [ ] 理解基本概念
- [ ] 掌握核心技能
- [ ] 完成实战练习

## 学习内容

### 基本概念

### 核心知识点

### 实战例子

## 笔记总结

## 复习建议

## 相关资源
- [官方文档]()
- [教程视频]()

## 掌握度评分

当前掌握度：⭐⭐⭐ (3/5)

```

### 步骤 2: 创建学习仪表板

`学习中心.md`：

```markdown
---
title: 学习中心
---

## 📚 学习概览

### 统计数据

\`\`\`dataview
TABLE 
  rating as "★",
  count(rows) as "数量"
FROM "学习笔记"
WHERE rating
GROUP BY rating
SORT rating DESC
\`\`\`

## 📊 掌握度排行

### 掌握最好（5★）
\`\`\`dataview
LIST file.name + ": " + rating
FROM "学习笔记"
WHERE rating = 5
SORT file.name
\`\`\`

### 需要加强（<3★）
\`\`\`dataview
LIST file.name + ": " + rating
FROM "学习笔记"
WHERE rating < 3
SORT rating ASC
\`\`\`

## 🔄 最近学习

\`\`\`dataview
TABLE updated, rating, status
FROM "学习笔记"
SORT updated DESC
LIMIT 15
\`\`\`

## 📈 学习统计

完全掌握：{{ sum(where(rows, r => r.rating = 5)) }} 个  
基本掌握：{{ sum(where(rows, r => r.rating >= 4)) }} 个  
学习中：{{ sum(where(rows, r => r.rating = 3)) }} 个  
需加强：{{ sum(where(rows, r => r.rating <= 2)) }} 个  

```

### 步骤 3: 日常使用流程

1. **新建笔记**：使用模板，评分初始为 0（计划学习）
2. **学习时更新**：完成学习后修改 `rating` 字段
3. **定期复习**：查看仪表板，找出评分低的笔记
4. **追踪进度**：每周检查统计数据

---

## 🎯 评分标准建议

### 推荐标准：5 星制

| ★ 数 | 掌握度 | 说明 | 行动 |
|------|--------|------|------|
| ⭐ (1) | 5% | 刚开始接触，只知道名词 | 开始学习 |
| ⭐⭐ (2) | 25% | 了解基本概念，无法应用 | 深入学习 |
| ⭐⭐⭐ (3) | 50% | 理解原理，能完成简单任务 | 实战练习 |
| ⭐⭐⭐⭐ (4) | 75% | 熟练应用，能解决多数问题 | 高级应用 |
| ⭐⭐⭐⭐⭐ (5) | 100% | 精通，可教别人 | 持续维护 |

### 也可使用百分比

```yaml
---
mastery: 75
---
```

然后用 Dataview 转换：

```dataview
TABLE mastery as "掌握度(%)"
FROM ""
WHERE mastery
SORT mastery DESC
```

---

## 🛠️ 高级技巧

### 组合多个评分维度

```yaml
---
title: JavaScript
rating: 4
difficulty: 3
importance: 5
fun: 4
---
```

```dataview
TABLE rating as "掌握度", difficulty as "难度", importance as "重要性"
FROM ""
WHERE rating
SORT rating DESC
```

### 按分类统计

```dataview
TABLE rating
FROM "学习笔记"
WHERE file.folder = "Frontend"
SORT rating DESC
```

### 创建学习路线图

```markdown
## 前端学习路线

\`\`\`dataview
TABLE rating as "掌握度"
FROM "学习笔记"
WHERE file.folder = "Frontend"
SORT file.name
\`\`\`

\`\`\`dataview
TABLE (5 - rating) as "还需努力"
FROM "学习笔记"  
WHERE file.folder = "Frontend" AND rating < 5
SORT (5 - rating) DESC
\`\`\`
```

---

## 💡 最佳实践

### 1. 定期复习
- 每周查看评分 < 3 的笔记
- 每月统计整体掌握度
- 按重要性优先学习

### 2. 合理评分
- 不要过度乐观，实事求是
- 定期重新评估
- 根据实际应用能力调整

### 3. 结合其他工具
```yaml
---
rating: 4
status: learning
next-review: 2025-12-15
practice-tasks:
  - 完成 5 个 LeetCode 题
  - 阅读官方文档第 3 章
---
```

### 4. 备份与导出
- 定期导出学习数据
- 用 Dataview 生成学习报告
- 分析学习效率

---

## 🔗 相关资源

- [Dataview 官方文档](https://blacksmithgu.github.io/obsidian-dataview/)
- [Obsidian 官方插件库](https://obsidian.md/plugins)
- [社区插件推荐](https://obsidian.md/community)

---

## 📋 快速对比

| 插件 | 功能 | 易用性 | 推荐度 |
|------|------|--------|--------|
| **Dataview** | 强大查询、统计 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Ratings** | 快速评分 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Progress Bar** | 进度条显示 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Spaced Repetition** | SRS 复习 | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Habits** | 习惯跟踪 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

**推荐组合**：Dataview + Ratings（最强 CP）

---

> **总结**：  
> 使用 Dataview + Ratings 组合，可以轻松构建一个完整的学习跟踪系统，  
> 实时了解自己的学习进度和知识掌握度。

---

**上级索引**：[[Obsidian]] | [[Resource]]
