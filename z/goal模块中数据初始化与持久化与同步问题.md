---
tags:
  - tech/lang/typescript
  - type/howto
  - status/growing
description: goal模块中数据初始化与持久化与同步问题
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]] | [[Goal 模块]]

---


# goal模块中数据初始化与持久化问题 

## 问题详情

用了 store persistence 插件，但是持久化中的数据缺少
## 解决方案
## 实战经验

现在是有一个初始化服务 和 store 中持久化

希望是能够从 store 中快速获取已有数据

## 同步

现在子实体发生变化会有副作用，影响到父实体、聚合根等，暂时采用从后端同步的方法：
我觉得应该使用事件总线，当 keyResult、goalRecord 等子实体更新是，统一发送一个 刷新 聚合根的事件，然后 监听 这个事件，触发同步服务（在 sync 中定义一个更新 goal 的服务
## 经验总结
## 信息参考
