---
tags:
  - tech/lang/nodejs
  - type/howto
  - status/growing
description: node-schedule Cron 作业调度器
created: 2025-07-16T10:32:12
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[JavaScript 工具库 MOC]] | [[Node.js MOC]]

---

# node-schedule 库的使用

[node-schedule Github](https://github.com/node-schedule/node-schedule)

- node-schedule 继承了 nodejs 中的 EventEmitter 类
- 有 run、scheduled、canceled、error、success 事件

### Cron 式调度

使用 Cron 格式的时间来调度任务：  

### 日期式调度

使用 Date 对象来调度任务：

### 循环规则调度

使用 rule 对象来调度任务：
