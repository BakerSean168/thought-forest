---
tags:
  - tech/lang/javascript
  - type/howto
  - status/growing
description: date-fns 日期时间处理库
created: 2025-07-25T20:21:40
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[JavaScript 工具库 MOC]] | [[ECMAScript MOC]]

---

# date-fns 库的使用

> 现代 JavaScript 日期处理库，模块化、轻量、不可变

[中文文档](https://date-fns.p6p.net/general/getting-started.html) | [官方文档](https://date-fns.org/)

---

## 安装

``bash
pnpm add date-fns
``

---

## 核心函数

### 格式化日期

``javascript
import { format, formatDistance, formatRelative } from 'date-fns'
import { zhCN } from 'date-fns/locale'

// 基础格式化
format(new Date(), 'yyyy-MM-dd HH:mm:ss')
// => "2025-12-08 14:30:00"

format(new Date(), 'yyyy年MM月dd日 EEEE', { locale: zhCN })
// => "2025年12月08日 星期日"
``

### 相对时间

``javascript
// 距离当前时间
formatDistance(new Date(2025, 11, 1), new Date(), { 
  addSuffix: true,
  locale: zhCN 
})
// => "7天前"

// 相对日期
formatRelative(new Date(2025, 11, 7), new Date(), { locale: zhCN })
// => "昨天 14:30"
``

---

## 常用格式化符号

| 符号 | 说明 | 示例 |
|------|------|------|
| `yyyy` | 4位年份 | 2025 |
| `MM` | 2位月份 | 12 |
| `dd` | 2位日期 | 08 |
| `HH` | 24小时制 | 14 |
| `mm` | 分钟 | 30 |
| `ss` | 秒 | 00 |
| `EEEE` | 星期全称 | 星期日 |
| `EEE` | 星期缩写 | 周日 |

---

## 日期计算

``javascript
import { addDays, subMonths, differenceInDays, isAfter } from 'date-fns'

// 加减日期
addDays(new Date(), 7)      // 7天后
subMonths(new Date(), 1)    // 1个月前

// 计算差值
differenceInDays(endDate, startDate)  // 相差天数

// 日期比较
isAfter(date1, date2)       // date1 是否在 date2 之后
``

---

## 与其他库对比

| 特性 | date-fns | Moment.js | Day.js |
|------|----------|-----------|--------|
| 体积 | 按需引入 | ~300KB | ~2KB |
| 不可变 | ✅ | ❌ | ✅ |
| Tree-shaking | ✅ | ❌ | ✅ |
| 维护状态 | 活跃 | 停止维护 | 活跃 |

---

## 相关笔记

- [[JavaScript 工具库 MOC]] - JS 工具库索引
- [[ECMAScript MOC]] - JavaScript 核心
