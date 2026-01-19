---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Tasks 插件 - 任务管理与追踪完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[工作流与自动化]]

---

# Tasks 插件详细指南

> **核心功能**：任务管理、优先级、截止日期、重复任务、查询  
> **难度级别**：⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：强烈推荐（任务管理标准）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 用途 |
|-----|------|------|
| **任务标记** | - [ ] 格式支持 | 记录待办 |
| **优先级** | 🔴🟡🟢 四级优先级 | 聚焦重点 |
| **截止日期** | 📅 日期管理 | 时间约束 |
| **重复任务** | 🔁 循环任务 | 习惯养成 |
| **任务查询** | 强大的查询语言 | 灵活筛选 |
| **块属性** | 📝 完整的任务属性 | 结构化管理 |
| **完成追踪** | ✅ 完成时间记录 | 进度追踪 |

### 任务格式

```markdown
- [ ] 基础任务

- [ ] 优先级任务 🔴高 🟡中 🟢低

- [ ] 有截止日期的任务 📅 2025-12-15

- [ ] 有提醒的任务 ⏰ 2025-12-10

- [ ] 重复任务 🔁 每周一

- [ ] 有标签的任务 #project #urgent

- [ ] 有完成日期的任务 ✅ 2025-12-08
```

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Tasks"
3. 安装并启用

### 基本配置

```yaml
Settings → Tasks

## 全局设置
Global Filter: ""                      # 全局过滤
Global Sort: ""                        # 全局排序

## 完成选项
Auto Set Done Date: true               # 完成自动添加日期
Minimum Due Date Interval: 0           # 最小截止日期间隔

## 显示选项
Show Edit Button: true                 # 显示编辑按钮
Show Backlinks: true                   # 显示反向链接
```

---

## 快速开始

### 创建任务

**基础任务**

```markdown
- [ ] 学习 Obsidian Tasks 插件
- [ ] 完成项目文档
- [ ] 健身房锻炼
```

**优先级任务**

```markdown
- [ ] 🔴 紧急 bug 修复（高优先级）
- [ ] 🟡 新功能开发（中优先级）
- [ ] 🟢 代码优化（低优先级）
```

**有截止日期的任务**

```markdown
- [ ] 提交报告 📅 2025-12-15
- [ ] 学习总结 📅 2025-12-20
- [ ] 项目评审 📅 2025-12-25
```

### 标记完成

```markdown
# 完成前
- [ ] 学习任务 📅 2025-12-15

# 完成后（点击复选框自动更新）
- [x] 学习任务 📅 2025-12-15 ✅ 2025-12-08

# 或手动标记
- [x] 学习任务 📅 2025-12-15 ✅ completed
```

---

## 工作流示例

### 场景 1：日常任务管理

**Daily Note 中的任务**

```markdown
---
date: 2025-12-08
---

# 2025-12-08 周一

## 今日任务

### 紧急
- [ ] 🔴 修复登录 bug

### 重要
- [ ] 🟡 代码审查
- [ ] 🟡 编写文档

### 普通
- [ ] 🟢 邮件回复
- [ ] 🟢 例会参加

### 完成情况
- [x] 晨会 ✅ 2025-12-08
- [x] 代码提交 ✅ 2025-12-08
```

### 场景 2：项目任务追踪

```markdown
# 项目：用户认证系统

## 任务清单

### 后端开发
- [ ] 🔴 用户注册 API 📅 2025-12-10 #backend
- [ ] 🔴 登录验证 📅 2025-12-10 #backend
- [ ] 🟡 权限管理 📅 2025-12-15 #backend

### 前端开发
- [ ] 🟡 登录页面 📅 2025-12-12 #frontend
- [ ] 🟡 用户资料页 📅 2025-12-15 #frontend
- [ ] 🟢 密码重置 📅 2025-12-20 #frontend

### 测试
- [ ] 🟡 单元测试 📅 2025-12-18 #testing
- [ ] 🟡 集成测试 📅 2025-12-20 #testing
```

### 场景 3：习惯追踪

```markdown
# 日常习惯

## 健身
- [ ] 晨跑 🔁 每天 ⏰ 06:00
- [ ] 瑜伽 🔁 工作日 ⏰ 20:00
- [ ] 力量训练 🔁 周一三五 ⏰ 18:00

## 学习
- [ ] 阅读 🔁 每天 📅 每晚 8:00-9:00
- [ ] 代码练习 🔁 每天 📅 每晚 9:00-10:00
- [ ] 笔记整理 🔁 周末

## 生活
- [ ] 冥想 🔁 每天 ⏰ 06:30
- [ ] 写日记 🔁 每天 ⏰ 21:00
- [ ] 清理房间 🔁 周日
```

---

## 任务查询语言

### 基础查询

```markdown
## 所有未完成的任务

​```tasks
not done
​```

## 紧急任务

​```tasks
priority is high
not done
​```

## 本周截止的任务

​```tasks
due before 2025-12-15
not done
​```

## 项目 A 的任务

​```tasks
tag includes #projectA
not done
​```
```

### 常用查询条件

```markdown
## 按优先级
priority is high / medium / none / low

## 按截止日期
due before 2025-12-15
due on 2025-12-08
due after 2025-12-01

## 按完成状态
done
not done

## 按标签
tag includes #urgent
tag does not include #done

## 按重复
recurrence includes every
no recurrence
```

### 组合查询

```markdown
## 复杂查询示例

# 本周紧急未完成任务
​```tasks
priority is high
not done
due before 2025-12-15
```

# 项目 A 中已完成的任务
​```tasks
tag includes #projectA
done
​```

# 今天应该完成的任务
​```tasks
due on 2025-12-08
not done
​```
```

---

## 常见问题

### Q1：如何创建重复任务？

**重复语法**

```markdown
- [ ] 晨跑 🔁 every day

- [ ] 周会议 🔁 every Monday

- [ ] 月度总结 🔁 every month on 1st

- [ ] 季度评估 🔁 every 3 months
```

### Q2：如何设置任务提醒？

**提醒选项**

```markdown
- [ ] 学习任务 ⏰ 2025-12-10 06:00

- [ ] 项目会议 ⏰ 2025-12-15 14:00

提醒时间在截止日期之前
```

### Q3：如何查询今天的所有任务？

```markdown
​```tasks
due on 2025-12-08
or no due date
not done
group by priority
sort by due
​```
```

### Q4：如何追踪任务完成时间？

```markdown
完成后自动记录：

- [x] 学习任务 📅 2025-12-15 ✅ 2025-12-08

✅ 后面的日期就是完成日期

也可以在周记中统计：
本周完成的任务数量
```

### Q5：Tasks 和 Kanban 如何选择？

```yaml
Tasks：
- 优点：轻量、快速、集成在笔记中
- 缺点：视觉化不如 Kanban
- 适合：以列表为主的管理

Kanban：
- 优点：可视化流程、看板视图
- 缺点：需要专门文件
- 适合：工作流管理、项目协作
```

---

## 高级技巧

### 1. 创建动态任务仪表板

```markdown
# 我的任务仪表板

## 今日任务

​```tasks
due on today
not done
sort by priority
​```

## 本周紧急

​```tasks
priority is high
due before next Sunday
not done
​```

## 逾期任务

​```tasks
due before today
not done
​```

## 下周计划

​```tasks
due after today and due before 2025-12-15
not done
sort by priority
​```
```

### 2. 使用块属性和自定义字段

```markdown
- [ ] 学习 React Hooks 📅 2025-12-15 #learning #urgent
  assignee:: 自己
  estimate:: 5h
  actual:: 3h
```

### 3. 与 Dataview 集成

```javascript
​```dataview
TABLE file.name, priority, due
FROM "Daily Notes"
WHERE contains(text, "- [ ]") AND !contains(text, "- [x]")
SORT due ASC
​```
```

---

## 最佳实践

1. **清晰的优先级分配**
   - 🔴 今天必须做
   - 🟡 这周要做
   - 🟢 有时间就做

2. **合理的截止日期**
   - 避免全部堆在一天
   - 留出缓冲时间
   - 重要任务提前规划

3. **定期回顾**
   - 每天早上检查当日任务
   - 每周回顾本周完成情况
   - 每月规划下月任务

4. **使用标签分类**
   - `#work #personal #learning`
   - 便于查询和统计

---

## 快速参考

### 常用快捷键

```yaml
标记完成：点击复选框
编辑任务：点击任务行
添加优先级：输入 🔴 或 🟡 或 🟢
添加日期：输入 📅 2025-12-15
```

---

## 📚 相关文档

- [[Kanban插件详细指南]] - 看板任务管理
- [[Periodic Notes插件详细指南]] - 周期任务规划
- [[Obsidian插件完全索引]] - 其他任务插件

---

**上级索引**：[[Obsidian插件完全索引]] | [[工作流与自动化]]
