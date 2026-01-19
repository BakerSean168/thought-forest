---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Database Folder 插件 - 文件夹数据库视图完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[数据与查询]]

---

# Database Folder 插件详细指南

> **核心功能**：文件夹即数据库、表格视图、行编辑、数据过滤  
> **难度级别**：⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：强烈推荐（结构化数据管理）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 优势 |
|-----|------|------|
| **文件夹数据库** | 将文件夹视为数据表 | 无需特殊配置，自动识别 |
| **表格视图** | 以表格形式显示笔记 | 一览无余，对比清晰 |
| **列管理** | 自定义显示哪些字段 | 灵活布局 |
| **行编辑** | 直接在表格中编辑数据 | 提高效率 |
| **排序与过滤** | 按条件排序和筛选 | 快速查找 |
| **批量操作** | 同时修改多行 | 节省时间 |
| **记录详情** | 点击行查看完整信息 | 上下文切换少 |

### Database Folder vs Dataview

| 项目 | Database Folder | Dataview |
|-----|-----------------|----------|
| 学习曲线 | 简单直观 | 需要学习 DQL |
| 编辑能力 | ✅ 可直接编辑 | ❌ 只读查询 |
| 定制灵活性 | 中等 | 很高 |
| 性能 | 快速 | 取决于查询复杂度 |
| 推荐用途 | 结构化数据管理 | 动态报表和汇总 |

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Database Folder"
3. 安装并启用

### 初始化配置

```yaml
Settings → Database Folder

## 全局设置
Enable Database Folder: true           # 启用插件
Auto Open Database View: true          # 自动打开数据库视图
Show File Name Column: true            # 显示文件名列
Show Created Date: true                # 显示创建日期
Show Modified Date: true               # 显示修改日期

## 显示选项
Record Fields Inline: false            # 不在列中显示完整内容
```

---

## 快速开始

### 创建数据库文件夹

**步骤 1：创建文件夹**

```
在 Obsidian 中：
1. 新建文件夹（如 "Books"）
2. Database Folder 自动识别
3. 在文件夹中创建笔记
4. 自动生成表格视图
```

**步骤 2：添加数据**

```markdown
# 书籍信息笔记

---
title: "深度学习"
author: "Ian Goodfellow"
year: 2016
rating: 5
status: "Reading"
---

内容描述...
```

**步骤 3：查看数据库视图**

```
文件夹上方自动出现"数据库视图"选项卡
以表格形式显示所有笔记的 YAML 字段
```

---

## 工作流示例

### 场景 1：书籍管理库

**文件夹结构**

```
Books/
├── 深度学习.md
├── Python编程.md
├── 数据科学手册.md
└── 系统设计.md
```

**表格视图**

| 标题 | 作者 | 年份 | 评分 | 状态 |
|------|------|------|------|------|
| 深度学习 | Ian | 2016 | ⭐⭐⭐⭐⭐ | 完成 |
| Python编程 | John | 2020 | ⭐⭐⭐⭐ | 阅读中 |
| 数据科学 | Maria | 2021 | ⭐⭐⭐⭐ | 计划中 |

**操作流程**

```
1. 添加新书：在 Books 文件夹新建笔记
2. 编辑信息：直接在表格中修改字段
3. 排序查看：按作者、评分排序
4. 快速过滤：只显示"完成"状态的书
5. 查看详情：点击行打开完整笔记
```

### 场景 2：项目追踪

**YAML 字段设定**

```markdown
---
title: "功能 A"
priority: 1
status: "In Progress"
assignee: "张三"
deadline: 2025-12-15
progress: 60
---
```

**表格界面**

| 项目名 | 优先级 | 状态 | 负责人 | 截止日 | 进度 |
|-------|-------|------|--------|--------|------|
| 功能 A | P1 | 进行中 | 张三 | 12-15 | 60% |
| 功能 B | P2 | 计划中 | 李四 | 12-20 | 0% |

**快速操作**

```
- 按优先级排序 → P1 优先处理
- 按状态过滤 → 只看"进行中"
- 编辑进度 → 直接在表格修改
- 批量更新 → 选中多行改状态
```

### 场景 3：客户管理

**字段设计**

```yaml
title: "客户名"
company: "公司"
email: "邮箱"
phone: "电话"
status: "活跃/非活跃"
last_contact: "最后联系时间"
deal_value: "交易金额"
```

**管理功能**

```
1. 表格视图快速查看所有客户
2. 按交易金额排序 → 识别高价值客户
3. 按联系时间过滤 → 找出需要跟进的
4. 直接编辑字段 → 更新客户信息
5. 点击行 → 查看详细联系记录
```

---

## 常见问题

### Q1：如何自定义列显示？

**配置步骤**

```
1. 打开数据库视图
2. 点击右上角 ⚙️ 设置按钮
3. "Visible Columns" → 选择要显示的字段
4. 拖拽列名调整顺序
5. 保存配置
```

### Q2：如何在表格中编辑数据？

**编辑方式**

```yaml
方式 1：直接编辑
- 点击表格单元格
- 修改内容
- 自动保存到笔记的 YAML

方式 2：编辑行
- 点击行名 → 打开笔记
- 编辑 YAML 字段
- 返回表格自动更新
```

### Q3：如何过滤和排序？

**使用方法**

```
过滤（Filter）：
1. 点击列名旁的漏斗图标
2. 设置条件（=, !=, >, <）
3. 表格自动过滤

排序（Sort）：
1. 点击列名
2. 选择升序/降序
3. 多列排序点击上下箭头
```

### Q4：如何批量编辑？

**批量操作**

```
1. 按住 Ctrl/Cmd + 点击选中多行
2. 或在行号处勾选
3. 右键 → Bulk Edit
4. 修改字段值
5. 应用到所有选中行
```

### Q5：新笔记的字段怎么自动填充？

**方案**

```yaml
使用 Templater 结合 Database Folder：

1. 在 Templates 文件夹创建模板
2. 模板包含标准 YAML 字段
3. 在 Database 文件夹中新建笔记时使用模板
4. 自动填充 YAML，表格自动显示
```

---

## 高级配置

### 1. 字段类型设定

```yaml
不同字段类型的配置：

TEXT：文本字段
  title: "项目名"

NUMBER：数字字段
  priority: 1
  progress: 60

DATE：日期字段
  deadline: 2025-12-15

SELECT：选择字段（预定义选项）
  status: "Active" | "Done" | "Paused"

CHECKBOX：复选框
  completed: true
```

### 2. 创建视图（不同的表格显示）

```
Settings → Database Folder → Views

创建多个视图：
- View 1：所有数据
- View 2：仅显示"完成"
- View 3：按优先级排序

快速切换不同视图
```

### 3. 公式字段（计算）

```
某些版本支持公式字段：

例如计算总分：
formula: "score1 + score2 + score3"

进度条显示：
formula: "(completed / total) * 100"
```

---

## 最佳实践

### 1. 规范字段命名

```yaml
✅ 推荐：
title, author, year, rating, status

❌ 避免：
标题, 作者, 年份, 评分, 状态（中文混乱）
T, A, Y, R, S（缩写不清）
```

### 2. 保持字段值一致

```yaml
✅ 推荐：
status: "Active" | "Inactive"（大小写一致）
priority: 1 | 2 | 3（统一类型）

❌ 避免：
status: "active" | "Active"（混乱）
priority: "1" | 2（类型不一致）
```

### 3. 定期备份

```
Database Folder 修改的是笔记中的 YAML
定期保存到 Git：
- 历史追踪
- 版本恢复
- 多设备同步
```

### 4. 组织文件夹结构

```
Knowledge Base/
├── Books/          → 书籍库
├── Projects/       → 项目库
├── Contacts/       → 联系人库
└── Articles/       → 文章库

每个文件夹独立管理
```

---

## 与其他插件配合

### Database Folder + Dataview

```
Database Folder：用于编辑结构化数据
Dataview：用于汇总查询数据

结合使用：
- 在 Database 中维护数据
- 用 Dataview 生成报表
```

### Database Folder + Templater

```javascript
// 在 Templater 中自动创建数据库笔记

<%* 
const dbFolder = "Books";
const title = tp.file.title;
-%>

---
title: "<%= title %>"
author: ""
year: <% new Date().getFullYear() %>
rating: 0
status: "Planned"
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <%= title %>

## 书籍信息

在这里添加笔记内容...
```

### Database Folder + Periodic Notes

```
Daily Note 中查询今日任务：
~~~dataview
TABLE status, progress
FROM "Projects"
WHERE assigned_date = date(today)
~~~
```

---

## 性能优化

### 优化建议

```yaml
对于大型数据库：

1. 列数不超过 20
   - 太多列会影响渲染速度
   - 使用视图只显示必要字段

2. 定期清理已完成项
   - 创建 "Archive" 文件夹
   - 移动已完成的笔记

3. 避免超大文件夹
   - 单文件夹笔记不超过 500
   - 使用子文件夹分散

4. 关闭不必要的默认列
   - File name, Created, Modified
   - Settings → 禁用不需要的
```

---

## 快速参考

### 常用操作速查表

```
新建记录：文件夹 → 新建笔记 → 输入 YAML → 自动显示
编辑字段：点击单元格 → 修改 → 回车确认
查看完整：点击行名 → 打开笔记 → 查看详情
排序数据：点击列名 → 升/降序
过滤数据：点击漏斗 → 设置条件
导出数据：右键 → Export as CSV
批量编辑：Ctrl+点击选多行 → 右键 → Bulk Edit
```

---

## 📚 相关文档

- [[Obsidian插件完全索引]] - 其他数据管理插件
- [[Dataview插件详细指南]] - 动态数据查询
- [[数据与查询]] - 数据管理概念

---

**上级索引**：[[Obsidian插件完全索引]] | [[数据与查询]]
