---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Calendar 插件 - 日历视图与日期导航完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[数据与查询]]

---

# Calendar 插件详细指南

> **核心功能**：日历视图、日期导航、日记管理、周期笔记  
> **难度级别**：⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：强烈推荐（日期管理必备）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 用途 |
|-----|------|------|
| **日历视图** | 月历格式显示 | 快速查看整月 |
| **日期导航** | 点击日期打开笔记 | 快速进入日记 |
| **笔记指示** | 有笔记的日期显示圆点 | 一目了然 |
| **周视图** | 周视图切换 | 周期规划 |
| **快速创建** | 直接创建日期相关笔记 | 减少步骤 |
| **多语言支持** | 中文支持 | 本地化 |
| **周期笔记** | 支持日/周/月/年 | 配合 Periodic Notes |

### 应用场景

| 场景 | 用途 |
|------|------|
| **日记管理** | 快速定位和创建日记 |
| **时间规划** | 月视图查看全月安排 |
| **学习追踪** | 查看学习记录分布 |
| **项目周期** | 跟踪项目进度 |
| **习惯追踪** | 可视化习惯养成 |

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Calendar"
3. 安装并启用

### 基本配置

```yaml
Settings → Calendar

## 显示设置
Show Week Number: true                 # 显示周数
Week Start On: "Monday"                # 周一开始

## 日期格式
Date Format: "YYYY-MM-DD"              # 日期显示格式
Confirm Before Creating: false         # 创建时不需确认

## 笔记文件夹
Daily Notes Folder: "Daily Notes"      # 日记存储位置
Weekly Notes Folder: "Weekly"          # 周记存储位置
Monthly Notes Folder: "Monthly"        # 月记存储位置
```

---

## 快速开始

### 打开日历

**方法 1：快捷键**

```yaml
Ctrl+Shift+C（Windows）
Cmd+Shift+C（Mac）
```

**方法 2：菜单打开**

```
View → Toggle Calendar
或右侧边栏直接显示
```

### 基本操作

| 操作 | 方法 | 效果 |
|------|------|------|
| 查看日期笔记 | 点击日期 | 打开该日期的笔记 |
| 创建笔记 | 右键日期 → Create | 创建该日期笔记 |
| 切换月份 | < > 按钮 | 前后翻页 |
| 返回今天 | "Today" 按钮 | 回到当月 |
| 周视图 | W 按钮 | 切换周视图 |

---

## 工作流示例

### 场景 1：日记管理

**日常流程**

```
早上：
1. 打开 Calendar
2. 点击今天日期
3. 自动打开 Daily Note
4. 记录日记、待办、总结

晚上：
1. 在日记中回顾一天
2. 标记完成的任务
3. 计划明天

周末：
1. Calendar → 周视图
2. 查看整周活动
3. 总结本周学习
```

**YAML 示例**

```markdown
---
date: 2025-12-08
mood: 😊
energy: 8/10
---

# 2025-12-08 周一

## 今日计划
- [ ] 完成项目 A
- [ ] 学习 DQL
- [ ] 锻炼 1h

## 今日记录
完成了...

## 今日反思
...
```

### 场景 2：学习追踪

**月视图追踪**

```
December 2025
Sun  Mon  Tue  Wed  Thu  Fri  Sat
                  1   •   2   •
 3   •   4   •   5   •   6   •
 7   •   8   •   9   •  10   •
11   •  12   •  13   •  14   •
15   •  16   •  17   •  18   •
19   •  20   •  21   •  22   •
23   •  24       25   •  26   •
27   •  28   •  29   •  30   •
31   •

圆点 = 有学习笔记的日期
一目了然看学习坚持度
```

**配置方案**

```yaml
Daily Notes Folder: "Daily Notes/学习"

每日记录：
- 学习时间
- 学习内容
- 掌握程度
- 遇到的问题

Calendar 自动统计，可视化学习频率
```

### 场景 3：周期笔记管理

**三层笔记体系**

```
Daily Note（日记）
↓ 汇总到
Weekly Note（周记）
↓ 汇总到
Monthly Note（月记）

Calendar 快速导航到任意层级
```

**周记示例**

```markdown
---
date: 2025-12-08
week: 49
---

# 第 49 周回顾 (12-08 ~ 12-14)

## 周计划完成情况
- [ ] 项目 A
- [x] 学习 B
- [ ] 优化 C

## 学习总结
本周学到...

## 下周计划
...
```

### 场景 4：习惯追踪

**习惯日记**

```markdown
---
date: 2025-12-08
---

# 习惯追踪

## 健身 ✅
- 晨跑 30 分钟
- 瑜伽 20 分钟

## 阅读 ✅
- 读书 1 小时

## 冥想 ❌
- 今天忘记了

## 今日连续天数
- 健身：34 天 🔥
- 阅读：28 天 🔥
- 冥想：5 天
```

**统计**

```
Calendar 显示：
- 每天有纪录 = 显示圆点
- 月底统计坚持率
- 可视化习惯养成
```

---

## 常见问题

### Q1：为什么没有显示日历面板？

**排查步骤**

```yaml
1. 确认插件已启用
   Settings → Community plugins → Calendar ✓

2. 打开日历面板
   View → Toggle Calendar
   或 Ctrl+Shift+C

3. 检查右侧边栏
   应该看到日历小部件

4. 重启 Obsidian
```

### Q2：日期笔记没有自动创建怎么办？

**原因 1：文件夹不存在**

```yaml
检查 Daily Notes 文件夹是否存在：
Settings → Calendar → Daily Notes Folder
文件夹不存在时自动创建或手动创建
```

**原因 2：Periodic Notes 冲突**

```yaml
如果装了 Periodic Notes 插件：
- 关闭 Calendar 的日记创建
- 改用 Periodic Notes 管理
- 两个插件只需一个负责创建
```

### Q3：如何改变日期格式？

**配置步骤**

```yaml
Settings → Calendar → Date Format

常用格式：
- YYYY-MM-DD：2025-12-08
- DD/MM/YYYY：08/12/2025
- YYYY年MM月DD日：2025年12月08日
```

### Q4：如何隐藏某些日期？

**方案**

```yaml
方法 1：使用文件夹过滤
Settings → Calendar → Exclude Folders

方法 2：使用前缀隐藏
文件名不以 YYYY-MM-DD 格式，日历不显示
```

### Q5：Calendar 和 Periodic Notes 如何搭配？

**推荐方案**

```yaml
分工：
- Calendar：快速导航和显示
- Periodic Notes：模板生成和管理

配置：
1. 关闭 Calendar 的自动创建
   Settings → Calendar → Confirm Before Creating: true

2. 启用 Periodic Notes 自动创建
   Settings → Periodic Notes → Auto Create

3. 使用 Calendar 快速打开任何日期笔记
   使用 Periodic Notes 提供的模板
```

---

## 高级技巧

### 1. 多颜色标记事件

```
使用不同颜色突出重要日期：

方案 1：使用 YAML 字段标记
---
event_type: "holiday" | "birthday" | "deadline"
---

方案 2：文件名约定
birthday-张三.md
deadline-项目提交.md
```

### 2. 日历视图定制

```yaml
Settings → Calendar → Week Numbers
显示周数便于周规划

Settings → Calendar → Week Starts On
选择周一还是周日作为周首
```

### 3. 快捷导航

```
快速打开特定日期：
- 按下箭头键 → 下个月
- 按上箭头键 → 上个月
- 点击"Today" → 回到今天
- 点击周数 → 周视图
```

---

## 与其他插件配合

### Calendar + Periodic Notes

```markdown
Periodic Notes 生成模板
Calendar 提供快速导航

Daily Note 模板（Periodic Notes）：
---
date: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.date.now("YYYY年MM月DD日 dddd") %>

Calendar 中点击日期 → 打开笔记
```

### Calendar + Dataview

```javascript
// 在月记中使用 Dataview 汇总日记

```dataview
LIST
FROM "Daily Notes"
WHERE date >= date(2025-12-01) AND date <= date(2025-12-31)
```
```

### Calendar + Habit Tracker

```
日历上用颜色显示习惯完成情况：
- 绿色：所有习惯都完成
- 黄色：部分完成
- 红色：未完成
```

---

## 最佳实践

1. **定期清理过期笔记**
   - 每季度整理一次
   - 归档旧日记到 Archive 文件夹
   - 保持活跃笔记库干净

2. **建立日记习惯**
   - 每天固定时间写日记
   - 周末写周记总结
   - 月底写月记回顾

3. **充分利用月视图**
   - 快速扫一眼整月
   - 看纪录分布找规律
   - 规划下月重点

4. **与任务系统整合**
   - Daily Note 中记录任务
   - Calendar 跟踪完成情况
   - 周末回顾总结

---

## 快速参考

### 常用操作速查

```
打开 Calendar：Ctrl+Shift+C
查看日记：点击日期
创建笔记：右键 → Create
上月：< 按钮
下月：> 按钮
今天：Today 按钮
周视图：W 按钮
月视图：M 按钮
```

---

## 📚 相关文档

- [[Periodic Notes插件详细指南]] - 周期笔记模板
- [[Obsidian插件完全索引]] - 其他日期相关插件
- [[Obsidian写作工作流]] - 日记工作流

---

**上级索引**：[[Obsidian插件完全索引]] | [[数据与查询]]
