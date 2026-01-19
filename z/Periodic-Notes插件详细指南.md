---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Periodic Notes 插件 - 周期笔记自动化完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[工作流与自动化]]

---

# Periodic Notes 插件详细指南

> **核心功能**：自动创建日记/周记/月记/年记、模板管理、快速打开  
> **难度级别**：⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：必装（周期笔记管理基础）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 优势 |
|-----|------|------|
| **自动创建** | 按日期自动创建笔记 | 无需手动操作 |
| **模板支持** | 使用 Templater 模板 | 统一格式 |
| **快速打开** | 快捷键快速打开笔记 | 提高效率 |
| **日期自动化** | 自动计算日期、周数、月份 | 准确无误 |
| **多层级** | 日/周/月/年独立管理 | 灵活层级 |
| **文件夹管理** | 自定义存储位置 | 组织清晰 |

### 层级关系

```
Daily Note（日记）
    ↓ 汇总到
Weekly Note（周记）
    ↓ 汇总到
Monthly Note（月记）
    ↓ 汇总到
Yearly Note（年记）
```

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Periodic Notes"
3. 安装并启用

### 核心配置

```yaml
Settings → Periodic Notes

## 日记（Daily）
Daily Notes → Enable: true
Daily Folder: "Daily Notes"
Daily Format: "YYYY-MM-DD"
Daily Template: "templates/daily"
Open Daily on Startup: false

## 周记（Weekly）
Weekly Notes → Enable: true
Weekly Folder: "Weekly Notes"
Weekly Format: "gggg-[W]ww"  # ISO 周格式
Weekly Template: "templates/weekly"
Weekly Start Day: "Monday"
Open on Startup: false

## 月记（Monthly）
Monthly Notes → Enable: true
Monthly Folder: "Monthly Notes"
Monthly Format: "YYYY-MM"
Monthly Template: "templates/monthly"
Open on Startup: false

## 年记（Yearly）
Yearly Notes → Enable: true
Yearly Folder: "Yearly Notes"
Yearly Format: "YYYY"
Yearly Template: "templates/yearly"
```

---

## 快速开始

### 创建模板

**Step 1：创建模板文件夹**

```
在 Obsidian 中创建：
Templates/
├── daily.md
├── weekly.md
├── monthly.md
└── yearly.md
```

**Step 2：编写日记模板**

```markdown
# Daily Template (templates/daily.md)

---
date: <% tp.date.now("YYYY-MM-DD") %>
day: <% tp.date.now("dddd") %>
---

# <% tp.date.now("YYYY年MM月DD日 dddd") %>

## 今日计划
- [ ] 
- [ ] 
- [ ] 

## 今日记录

## 今日反思

## 明日预告
```

**Step 3：配置指向模板**

```yaml
Settings → Periodic Notes
Daily Template: "Templates/daily"
```

### 快捷键配置

```yaml
设置快速打开快捷键：
Settings → Hotkeys

搜索 "Periodic Notes"：
- Open Daily Note: Ctrl+Alt+D
- Open Weekly Note: Ctrl+Alt+W
- Open Monthly Note: Ctrl+Alt+M
- Open Yearly Note: Ctrl+Alt+Y
```

---

## 工作流示例

### 场景 1：完整日常工作流

**早上**

```
1. 打开 Obsidian
2. Ctrl+Alt+D 打开今日 Daily Note
3. 自动使用模板创建（如果不存在）
4. 填写今日计划
```

**工作中**

```
5. 在 Daily Note 中记录进度
6. 更新任务完成情况
```

**晚上**

```
7. 完成每日反思
8. 摘要记录关键学习
9. 规划明天
```

**周末**

```
10. Ctrl+Alt+W 打开周记
11. 汇总一周的学习和完成情况
12. 周回顾和下周规划
```

### 场景 2：日记汇总体系

**日记模板**

```markdown
---
date: 2025-12-08
mood: 😊
---

# 2025-12-08

## 学习时间
- React Hooks：2h
- TypeScript：1.5h

## 完成任务
- [x] 代码审查
- [x] 文档更新

## 遇到的问题
- 组件渲染问题

## 明日计划
- 继续学习
- 完成功能开发
```

**周记汇总**

```markdown
---
date: 2025-12-08
week: 49
---

# 第 49 周总结

## 学习统计
- 总学习时间：23.5h
- 平均每天：3.4h
- 最高纪录：2025-12-10（5h）

## 关键收获
从日记汇总：
<%* 
const dailies = dv.pages('"Daily Notes"')
  .where(p => p.date >= date(2025-12-08) && p.date <= date(2025-12-14));
-%>

​```dataview
LIST 
FROM "Daily Notes"
WHERE date >= date(2025-12-08) AND date <= date(2025-12-14)
​```

## 下周计划
...
```

---

## 常见问题

### Q1：为什么笔记没有自动创建？

**检查清单**

```yaml
1. 确认 Periodic Notes 已启用
   Settings → Community plugins → Periodic Notes ✓

2. 确认相应类型已启用
   Settings → Periodic Notes → Daily Notes → Enable: true

3. 检查模板是否存在
   Daily Template 路径是否正确

4. 检查文件夹是否存在
   Daily Folder 文件夹是否已创建

5. 手动创建
   菜单 → Open Daily Note
   或快捷键 Ctrl+Alt+D
```

### Q2：日期格式错误怎么办？

**常见格式**

```yaml
Daily（日记）：
- YYYY-MM-DD：2025-12-08
- DD-MM-YYYY：08-12-2025
- YYYY/MM/DD：2025/12/08

Weekly（周记）：
- gggg-[W]ww：2025-W49（ISO 标准）
- YYYY-Www：2025-W49
- YYYYWW：202549

Monthly（月记）：
- YYYY-MM：2025-12
- YYYY/MM：2025/12
- YYYY年MM月：2025年12月

Yearly（年记）：
- YYYY：2025
- YYYY年：2025年
```

### Q3：模板变量怎么用？

**Templater 变量**

```markdown
在模板中使用：

日期变量：
<% tp.date.now("YYYY-MM-DD") %>  # 当前日期

周数变量：
<% tp.date.now("W") %>  # 周数

月份变量：
<% tp.date.now("MMMM") %>  # 完整月份名

自定义变量：
<% tp.date.now("YYYY年MM月DD日 dddd") %>
```

### Q4：如何在日记中自动链接到周记？

**解决方案**

```markdown
在 Daily 模板中添加：

## 周记链接
[[<%+ tp.date.now("gggg-[W]ww") %>|本周周记]]

在 Weekly 模板中添加：

## 本周日记
<%* 
// 自动列出本周的日记
-%>
```

### Q5：如何禁用某些笔记类型的自动创建？

```yaml
Settings → Periodic Notes
只启用需要的类型：
- Daily Notes → Enable: true
- Weekly Notes → Enable: false
- Monthly Notes → Enable: true
- Yearly Notes → Enable: false
```

---

## 高级配置

### 1. 多层级模板链接

**Daily 模板**

```markdown
---
date: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.date.now("YYYY-MM-DD dddd") %>

[[<%+ tp.date.now("gggg-[W]ww") %>|→ 查看本周]]

## 今日计划
...
```

**Weekly 模板**

```markdown
---
week: <% tp.date.now("gggg-[W]ww") %>
---

# 第 <% tp.date.now("W") %> 周

[[<%+ tp.date.now("YYYY-MM") %>|→ 查看本月]]

本周日记：
​```dataview
LIST
FROM "Daily Notes"
WHERE date >= date(<% tp.date.now("YYYY-MM-DD") %>) - 7 days
AND date <= date(<% tp.date.now("YYYY-MM-DD") %>)
```

## 周总结
...
```

**Monthly 模板**

```markdown
---
month: <% tp.date.now("YYYY-MM") %>
---

# <% tp.date.now("YYYY年MM月") %>

[[<%+ tp.date.now("YYYY") %>|→ 查看本年]]

本月周记：
​```dataview
LIST
FROM "Weekly Notes"
WHERE date_format(date, "yyyy-MM") = "<% tp.date.now("YYYY-MM") %>"
```

## 月总结
...
```

### 2. 自动统计功能

```javascript
// 在周记中自动统计学习时间

​```dataview
LIST rows.file.name
FROM "Daily Notes"
WHERE date >= date(now) - dur(7 days) AND status = "completed"
GROUP BY date
```

或使用 Dataview 求和：

​```dataview
TABLE sum(rows.hours) as "总时数"
FROM "Daily Notes"
WHERE date >= date(now) - dur(7 days)
GROUP BY category
```
```

---

## 最佳实践

1. **统一模板格式**
   - 所有日记使用同一模板
   - 保证字段名一致
   - 便于后续聚合和查询

2. **定期回顾**
   - 每周看一次周记
   - 每月看一次月记
   - 每年年底回顾全年

3. **关键信息突出**
   - 使用 Callout 突出重要事项
   - 用标签分类内容
   - 便于后续查找

4. **链接关联**
   - 日记中链接到相关笔记
   - 形成知识网络
   - 提高复习效率

---

## 与其他插件配合

### Periodic Notes + Calendar

```yaml
Periodic Notes：负责创建和管理
Calendar：负责快速导航

快捷键配置：
Ctrl+Alt+D 打开日记（Periodic Notes）
Ctrl+Shift+C 打开日历（Calendar）
点击日历日期也能打开笔记
```

### Periodic Notes + Templater

```markdown
完整集成示例：

模板中的高级特性：
- 动态日期计算
- 自动链接生成
- 条件逻辑（if/else）
- 文件操作
- 用户输入
```

### Periodic Notes + Dataview

```javascript
// 在周记中查询该周所有笔记

​```dataview
TABLE file.name, tags, status
FROM "Daily Notes"
WHERE date >= date(now) - dur(7 days)
SORT date DESC
```
```

---

## 快速参考

### 常用快捷键

```yaml
打开日记：Ctrl+Alt+D
打开周记：Ctrl+Alt+W
打开月记：Ctrl+Alt+M
打开年记：Ctrl+Alt+Y
```

### 常用格式

```yaml
日期：YYYY-MM-DD
周数：gggg-[W]ww
月份：YYYY-MM
年份：YYYY
```

---

## 📚 相关文档

- [[Templater插件详细指南]] - 模板编写详解
- [[Calendar插件详细指南]] - 日历快速导航
- [[Obsidian写作工作流]] - 完整工作流示例

---

**上级索引**：[[Obsidian插件完全索引]] | [[工作流与自动化]]
