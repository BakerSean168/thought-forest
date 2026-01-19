---
tags:
  - tech/dev/pm
  - type/howto
  - status/seed
description: Vue项目通知模块设计
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]]

---

## 目标

我需要一个独立的 提醒模块，用于弹窗、发出声音、发邮件等提醒功能。

在当前系统中的使用：  
schedule 时间到后，根据设定的提醒方式，再通过事件系统让 notification 模块进行提醒
先实现弹窗、发声，不是窗口内的弹窗，是系统级的弹窗，桌面右下角的弹窗

