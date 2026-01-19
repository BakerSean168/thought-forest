---
tags:
  - tech/dev/pm
  - type/howto
  - status/growing
description: Task模块-任务管理业务理解
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]]

---



# 业务理解

## Task模块目标

1. 支持重复任务、单次任务（比如 跑步任务，每天 8点到9点）
2. 任务应该有起止时间，支持无期限任务
3. 支持区分时间段任务（单天内的，不支持跨天）、时间点任务、全天任务，
4. 支持提醒功能，任务开始前几分钟之类的预设值
5. 支持与 goal 模块的 keyResult 绑定，完成时自动增加 keyResult 完成记录（即创建 goalRecord）
6. 我个人任务应该使用 任务模板-任务实例 + 任务元模板 的形式，你用没有更好的架构

## 关键点

### task 和 keyresult联动时的 完成 功能的业务问题


**问题**：
现在 complete 任务实例是默认根据 taskTemplate 创建时绑定的 关键结果值 来变化的，并且点击一下 按钮就生效，还支持 undo（还没准确实现）

我觉得当前的设计不好，因为关键结果是有多种计算方式，比如 普通的 累加，还有 取最大值、取平均值，完成时 比如当 绑定的关键结果为 考试最高分到达 90，每月进行一次考试 时，用户完成 考试任务，并标记完成，添加到新的 record 应该需要 用户根据本次的考试结果来确定，而非默认可以达到； 并且如果实现每次完成都需要弹窗确认并输入新的 record 的值，可以确保 用户不会误触，也就**不需要 undo 功能**（并不好实现）

**解决方法**：
所以应该取消默认的 record 值，改为用户点击 complete 时，弹窗显示相关信息，并让用户手动输入 新 record 的值
你觉得呢

## CRUD

### 创建 taskTemplate

1. 前端 TaskTemplateDialog 组件通过暴露的 openForCreation 方法打开，内部通过 TaskTemplate 的 forCreate 方法生成一个初始化的 taskTemplate 对象，并通过 computed 的 get、set 方法通过表单编辑。
2. 点击保存时，调用使用task服务的 composables 中的 createTaskTemplate 方法来（component - composables - service - api)调用接口。
3. (route - controller - applicationService - domainService - repo)后端调用服务方法还有TaskTemplate聚合根内部的方法（创建工厂方法），在生成 TaskTemplate 后，也使用相应方法来生成 TaskInstance 并 聚合根式地通过 TaskTemplate 仓储接口来保存（调用 toPersistenceDTO，递归式地把聚合根和旗下子实体一起转为持久化数据结构）
4. 服务端返回 完整地聚合根包含子实体（通过toClientDTO，来递归地把聚合根和旗下子实体一起转为客户端的实体DTO数据结构）
5. 客户端通过 fromClientDTO，转为实体对象，并更新 pinia 中的状态存储
6. 渲染页面通过  composables 中从 store 中响应式获取的 taskTemplate 数据来渲染
