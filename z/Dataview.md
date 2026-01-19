---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Dataview插件详解 - 将知识库变成可查询的数据库
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[Obsidian的插件]]

---

# Dataview 插件详解

## 1. 概览（Overview）

**Dataview** 是 Obsidian 最强大的社区插件之一，它将你的知识库变成一个**可查询的数据库**。通过类似 SQL 的查询语言，你可以动态地展示、过滤、排序笔记内容。

### 核心价值

| 传统方式 | Dataview 方式 |
|---------|--------------|
| 手动维护链接列表 | 自动生成动态列表 |
| 复制粘贴更新 | 查询结果实时更新 |
| 难以跨笔记统计 | 轻松聚合多个笔记的数据 |

### 适用场景

- 📋 自动生成待办事项汇总
- 📚 按标签/日期筛选笔记
- 📊 统计知识库数据
- 🔗 创建动态的 MOC 索引页

---

## 2. 安装与配置

### 安装步骤

1. 打开 Obsidian 设置 → **Community plugins**
2. 关闭 **Safe mode**
3. 点击 **Browse** → 搜索 "Dataview"
4. 点击 **Install** → **Enable**

### 推荐配置

在 Dataview 设置中：
- ✅ **Enable JavaScript Queries** - 启用更强大的 JS 查询
- ✅ **Enable Inline Queries** - 启用行内查询
- ⚙️ **Refresh Interval** - 建议设为 2500ms

---

## 3. 核心概念

### 数据来源

Dataview 从以下位置提取数据：

```yaml
---
# Frontmatter（YAML格式）
tags: [tech/lang/vue, type/concept]
status: growing
created: 2025-01-01
priority: high
---
```

```markdown
# 正文中的内联字段
完成日期:: 2025-12-08
评分:: ⭐⭐⭐⭐⭐
```

### 查询类型

| 类型 | 语法 | 用途 |
|------|------|------|
| **TABLE** | `TABLE field1, field2` | 表格展示 |
| **LIST** | `LIST` | 列表展示 |
| **TASK** | `TASK` | 任务列表 |
| **CALENDAR** | `CALENDAR date` | 日历视图 |

---

## 4. 快速开始

### 基础语法结构

````markdown
```dataview
<查询类型>
FROM <数据源>
WHERE <筛选条件>
SORT <排序字段> ASC/DESC
LIMIT <数量限制>
```
````

### 最小示例

**列出所有带 `#tech/lang/vue` 标签的笔记：**

````markdown
```dataview
LIST
FROM #tech/lang/vue
```
````

**表格展示最近修改的10篇笔记：**

````markdown
```dataview
TABLE file.mtime AS "修改时间", description AS "描述"
FROM "z"
SORT file.mtime DESC
LIMIT 10
```
````

---

## 5. 实用查询示例

### 5.1 按标签筛选

**列出所有概念类笔记：**

````markdown
```dataview
LIST
FROM #type/concept
SORT file.name ASC
```
````

**列出 Vue 相关的教程：**

````markdown
```dataview
TABLE description AS "描述"
FROM #tech/lang/vue AND #type/howto
```
````

### 5.2 按状态筛选

**查看所有待完善的笔记（种子状态）：**

````markdown
```dataview
LIST
FROM #status/seed
SORT file.ctime DESC
```
````

**查看正在写的笔记：**

````markdown
```dataview
TABLE file.mtime AS "最后修改"
FROM #status/growing
SORT file.mtime DESC
LIMIT 20
```
````

### 5.3 按文件夹/路径筛选

**列出 z 文件夹下的所有 MOC：**

````markdown
```dataview
LIST
FROM "z"
WHERE contains(file.name, "MOC")
```
````

### 5.4 按时间筛选

**最近7天修改的笔记：**

````markdown
```dataview
TABLE file.mtime AS "修改时间"
FROM "z"
WHERE file.mtime >= date(today) - dur(7 days)
SORT file.mtime DESC
```
````

**今天创建的笔记：**

````markdown
```dataview
LIST
FROM "z"
WHERE file.cday = date(today)
```
````

### 5.5 统计查询

**按领域标签统计笔记数量：**

````markdown
```dataview
TABLE length(rows) AS "笔记数"
FROM #tech
GROUP BY file.tags[0]
SORT length(rows) DESC
```
````

### 5.6 任务汇总

**汇总所有未完成的任务：**

````markdown
```dataview
TASK
FROM "z"
WHERE !completed
GROUP BY file.link
```
````

---

## 6. 高级用法

### 6.1 行内查询

在正文中直接嵌入查询结果：

```markdown
知识库共有 `= length(dv.pages())` 篇笔记。

Vue 相关笔记有 `= length(dv.pages("#tech/lang/vue"))` 篇。
```

### 6.2 JavaScript 查询（DataviewJS）

更灵活的查询方式：

````markdown
```dataviewjs
// 获取所有 seed 状态的笔记并按创建时间排序
let seeds = dv.pages("#status/seed")
    .sort(p => p.file.ctime, 'desc')
    .limit(10);

dv.table(
    ["笔记", "创建时间", "标签"],
    seeds.map(p => [
        p.file.link,
        p.file.ctime,
        p.tags?.join(", ") || "-"
    ])
);
```
````

### 6.3 条件组合

**复杂筛选条件：**

````markdown
```dataview
TABLE description, file.mtime AS "更新时间"
FROM #tech/lang/javascript OR #tech/lang/typescript
WHERE status != "archived"
SORT file.mtime DESC
LIMIT 20
```
````

---

## 7. 常用内置字段

### 文件元数据（file.*）

| 字段 | 说明 | 示例 |
|------|------|------|
| `file.name` | 文件名（不含扩展名）| "Vue3响应式原理" |
| `file.path` | 完整路径 | "z/Vue3响应式原理.md" |
| `file.link` | 可点击的链接 | [[Vue3响应式原理]] |
| `file.size` | 文件大小（字节）| 2048 |
| `file.ctime` | 创建时间 | 2025-01-01 |
| `file.cday` | 创建日期 | 2025-01-01 |
| `file.mtime` | 修改时间 | 2025-12-08 |
| `file.mday` | 修改日期 | 2025-12-08 |
| `file.tags` | 标签数组 | ["tech/vue", "type/concept"] |
| `file.etags` | 完整标签（含嵌套）| |
| `file.inlinks` | 入链（指向此文件的链接）| |
| `file.outlinks` | 出链（此文件指向的链接）| |
| `file.tasks` | 文件中的任务列表 | |

---

## 8. 在 MOC 中的实际应用

### 动态生成 Vue 笔记索引

在 `Vue3 MOC.md` 中使用：

````markdown
## 📚 所有 Vue 相关笔记

```dataview
TABLE 
    description AS "描述",
    file.mtime AS "更新时间"
FROM #tech/lang/vue
WHERE file.name != "Vue3 MOC"
SORT file.mtime DESC
```
````

### 知识库健康检查

````markdown
## 🌱 待完善的笔记（种子状态）

```dataview
LIST
FROM #status/seed
LIMIT 20
```

## 🔗 孤岛笔记（无入链）

```dataview
LIST
FROM "z"
WHERE length(file.inlinks) = 0 AND !contains(file.name, "MOC")
LIMIT 20
```
````

---

## 9. 常见问题

### Q: 查询结果为空？

检查：
1. 标签拼写是否正确（区分大小写）
2. FROM 路径是否正确
3. 是否有满足 WHERE 条件的笔记

### Q: 查询太慢？

解决：
1. 缩小 FROM 范围
2. 添加 LIMIT 限制
3. 调整设置中的刷新间隔

### Q: 如何调试查询？

使用 DataviewJS 的 `dv.paragraph()` 输出调试信息：

````markdown
```dataviewjs
let pages = dv.pages("#tech/lang/vue");
dv.paragraph(`找到 ${pages.length} 篇笔记`);
```
````

---

## 10. 相关链接

- [Dataview 官方文档](https://blacksmithgu.github.io/obsidian-dataview/)
- [Dataview 查询示例库](https://github.com/blacksmithgu/obsidian-dataview/discussions)
- [[Obsidian的插件]] - 更多插件推荐
- [[知识库管理体系 MOC]] - 知识库管理方法

