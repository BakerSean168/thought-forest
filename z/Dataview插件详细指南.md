---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Dataview 插件 - 动态数据查询完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[数据与查询]]

---

# Dataview 插件详细指南

> **核心功能**：DQL 查询语言、动态表格/列表/任务视图、实时数据更新  
> **难度级别**：⭐⭐⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：必装（动态知识库基础）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 用途 |
|-----|------|------|
| **DQL 查询** | Dataview Query Language 查询语言 | 灵活筛选与聚合数据 |
| **TABLE 视图** | 表格形式展现结果 | 数据对比与分析 |
| **LIST 视图** | 列表形式展现结果 | 内容浏览 |
| **TASK 视图** | 任务列表展现 | 任务管理 |
| **CALENDAR 视图** | 日历形式展现 | 时间规划 |
| **Inline Queries** | 内联查询 | 笔记内部实时数据 |
| **JavaScript API** | 更强大的编程接口 | 高级定制 |

### 数据源

Dataview 可以查询的数据：
- YAML frontmatter（元数据）
- Inline dataview fields（内联字段）
- 笔记文件名
- 笔记路径
- 修改/创建时间

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Dataview"
3. 安装并启用

### 基本配置

```yaml
Settings → Dataview

## 核心选项
Enable Inline Queries: true              # 启用内联查询
Enable JavaScript Queries: true          # 启用 JS 查询
Enable Inline JS Queries: true           # 启用内联 JS 查询
Render Inline Field Values: true         # 渲染内联字段值

## 性能优化
Max Recursive Depth: 10                  # 最大递归深度
Index All Values: true                   # 索引所有值
```

---

## DQL 查询语言

### 基础语法

```dataview
TABLE
  file.name,
  status,
  priority
FROM #project
WHERE status != "Done"
GROUP BY priority
SORT file.mtime DESC
```

### 查询结构

```
[VIEW_TYPE]
  [列定义]
FROM [源]
WHERE [条件]
GROUP BY [分组]
SORT [排序]
LIMIT [数量限制]
```

---

### 1. 基础 TABLE 查询

**查询所有项目笔记的标题和状态**

```dataview
TABLE title, status
FROM "Projects"
```

**输出**

| File | title | status |
|------|-------|--------|
| Project A | Project A | Active |
| Project B | Project B | Done |

### 2. 条件查询（WHERE）

**查询所有未完成的任务**

```dataview
TABLE title, deadline, priority
FROM #todo
WHERE status != "Done"
SORT deadline ASC
```

**查询特定日期范围内创建的笔记**

```dataview
TABLE title, created
FROM ""
WHERE created >= date(2025-01-01) AND created <= date(2025-12-31)
```

### 3. 分组查询（GROUP BY）

**按状态分组查看项目**

```dataview
TABLE rows.file.name
FROM #project
GROUP BY status
```

**按优先级分组统计任务数**

```dataview
TABLE length(rows) as "任务数"
FROM #task
GROUP BY priority
```

### 4. 聚合函数

**统计笔记数量**

```dataview
TABLE length(rows) as "笔记数"
FROM "学习笔记"
```

**计算平均分数**

```dataview
TABLE average(rows.score) as "平均分"
FROM #exam
GROUP BY subject
```

**常用聚合函数**

| 函数 | 说明 | 示例 |
|-----|------|------|
| `length(rows)` | 行数 | `length(rows) as count` |
| `average(rows.field)` | 平均值 | `average(rows.score)` |
| `sum(rows.field)` | 求和 | `sum(rows.points)` |
| `min(rows.field)` | 最小值 | `min(rows.date)` |
| `max(rows.field)` | 最大值 | `max(rows.priority)` |

### 5. 列表视图（LIST）

**查询所有标签**

```dataview
LIST
FROM ""
WHERE file.tags
```

**显示最近修改的笔记**

```dataview
LIST file.mtime
FROM ""
SORT file.mtime DESC
LIMIT 10
```

### 6. 任务视图（TASK）

**查询所有未完成的任务**

```dataview
TASK
FROM #todo
WHERE !completed
SORT due DESC
```

**按标签分类任务**

```dataview
TASK
FROM ""
WHERE tags
GROUP BY tags
```

---

## 内联查询

### 内联字段定义

```markdown
---
author: John Doe
rating:: 5
---

# 笔记标题

这是一个 [字段名:: 字段值] 的内联字段。
```

### 内联数据查询

**在笔记中显示特定笔记的信息**

```markdown
这本书的评分是 `=dv.pages("#book").where(p => p.file.name == "书名").map(p => p.rating)`
```

### 内联查询示例

**显示今日任务数**

```markdown
今天有 `=dv.pages("#todo").where(p => p.due == dv.date.now()).length` 个任务
```

**显示项目进度**

```markdown
完成率：`=dv.pages("#project").where(p => p.file.name == "项目名").map(p => p.progress)[0]`%
```

---

## 常见查询模板

### 模板 1：学习进度追踪

```dataview
TABLE 
  title,
  progress,
  next_review,
  mastery
FROM #study
WHERE mastery < 5
SORT next_review ASC
```

**用途**：查看需要复习的内容，按复习时间排序

### 模板 2：项目管理

```dataview
TABLE
  title,
  status,
  team_member,
  deadline,
  progress
FROM #project
WHERE status != "Done"
GROUP BY team_member
SORT deadline ASC
```

**用途**：团队项目分配与进度跟踪

### 模板 3：阅读计划

```dataview
TABLE
  title,
  author,
  status,
  progress,
  rating
FROM #book
WHERE status = "Reading" OR status = "Planned"
SORT status DESC
```

**用途**：管理阅读清单与进度

### 模板 4：知识库统计

```dataview
TABLE
  length(rows) as "笔记数",
  average(rows.confidence) as "平均掌握度"
FROM ""
GROUP BY file.folder
```

**用途**：统计各文件夹知识库规模与掌握情况

### 模板 5：最近活动

```dataview
TABLE
  title,
  created,
  file.mtime
FROM ""
WHERE file.mtime >= date(today) - dur(7 days)
SORT file.mtime DESC
```

**用途**：查看最近一周创建或修改的笔记

---

## 日期与时间函数

### 常用日期函数

| 函数 | 说明 | 示例 |
|-----|------|------|
| `date(today)` | 今天 | `due = date(today)` |
| `date(2025-12-08)` | 特定日期 | `created >= date(2025-01-01)` |
| `now()` | 当前时间 | `updated = now()` |
| `dur()` | 时间段 | `date(today) - dur(7 days)` |

### 日期范围查询

**查询最近 7 天创建的笔记**

```dataview
TABLE title, created
FROM ""
WHERE created >= date(today) - dur(7 days)
SORT created DESC
```

**查询本月的任务**

```dataview
TABLE title, due
FROM #todo
WHERE due.month = date(today).month
SORT due ASC
```

---

## 常见问题

### Q1：查询结果为空怎么办？

**排查步骤**

```
1. 检查 FROM 路径是否正确
   - FROM #tag → 标签是否真的存在
   - FROM "folder" → 文件夹路径是否正确

2. 检查字段名是否正确
   TABLE status → 笔记中是否真的有 status 字段

3. 检查条件语法
   WHERE status != "Done" → != 是否是正确的比较符

4. 启用调试日志
   Settings → Dataview → Debug Queries
```

### Q2：如何查询包含某个链接的笔记？

```dataview
TABLE file.links
FROM ""
WHERE contains(file.links, [[目标笔记]])
```

### Q3：如何按日期分组统计？

```dataview
TABLE length(rows) as "数量"
FROM #event
GROUP BY dateformat(date, "yyyy-MM")
```

### Q4：如何合并多个条件？

```dataview
TABLE title, status
FROM #project
WHERE (status = "Active" OR status = "Review") 
  AND priority >= 3
```

### Q5：性能优化：查询很慢怎么办？

**优化方案**

```yaml
1. 减少 FROM 范围
   FROM #tag 比 FROM "" 更快

2. 使用 LIMIT 限制结果数量
   LIMIT 50

3. 避免复杂嵌套查询

4. 禁用不需要的索引
   Settings → Dataview → Index All Values: false
```

---

## JavaScript API（高级）

### 基础 JS 查询

```javascript
const pages = dv.pages("#project");
const active = pages.where(p => p.status == "Active");
dv.table(
  ["title", "progress"],
  active.map(p => [p.file.name, p.progress])
);
```

### 常用 API

```javascript
// 获取所有页面
const allPages = dv.pages();

// 按条件筛选
const filtered = allPages.where(p => p.priority > 3);

// 映射数据
const names = pages.map(p => p.file.name);

// 排序
const sorted = pages.sort(p => p.due);

// 分组
const grouped = pages.groupBy(p => p.status);

// 渲染表格
dv.table(["列1", "列2"], pages.map(p => [p.title, p.status]));

// 渲染列表
dv.list(pages.map(p => p.file.link));
```

### 实例：动态进度条

```javascript
const projects = dv.pages("#project");
dv.table(
  ["项目", "进度"],
  projects.map(p => {
    const progress = p.progress || 0;
    const bar = "█".repeat(progress / 10) + "░".repeat(10 - progress / 10);
    return [p.file.link, `${bar} ${progress}%`];
  })
);
```

---

## 与其他插件配合

### Dataview + Templater

```markdown
<%* 
const notes = dv.pages("#recent").limit(5);
-%>

## 最近更新

<% notes.map(n => `- [[${n.file.name}]]`).join('\n') %>
```

### Dataview + Periodic Notes

```
在 Daily Note 中使用 Dataview 查询今日任务、事件等
```

---

## 最佳实践

1. **规范命名字段**
   - 使用一致的字段名：status, priority, due
   - 便于查询和维护

2. **充分利用元数据**
   - 添加必要的 YAML 字段
   - 便于后续查询和聚合

3. **创建查询库**
   - 建立 "查询" 文件夹
   - 保存常用查询模板

4. **性能考虑**
   - 避免查询整个库
   - 使用标签和文件夹限制范围

5. **测试查询**
   - 先在小范围测试
   - 再推广到大范围

---

## 📚 相关文档

- [[Obsidian学习追踪系统]] - 使用 Dataview 构建学习系统
- [[Obsidian插件完全索引]] - 其他查询和数据插件
- [[数据与查询]]

---

**上级索引**：[[Obsidian插件完全索引]] | [[数据与查询]]
