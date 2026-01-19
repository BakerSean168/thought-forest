---
tags:
  - tech/dev/pm
  - type/howto
  - status/growing
description: Schedule模块-实现过程的一些思考。记录实现的时候的思想，设计模式
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]]

---


「Schedule」模块是一个基础模块，为其他模块提供基础服务。  


## ScheduleTask对象

现在项目应该是还没有为 task、reminder 等模块集成 schedule 功能，我们现在先来讨论一下怎么集成，来实现 提醒到事件时，弹窗或者发声音来提醒用户 的功能。
已经有了初步的 schedule、task 等，要怎么连接起来。
我的想法：  
schedule 设置一个 eventhandler，来监听其他模块的请求生成队列的事件，生成一个对应 schedule 的实体对象，实体对象管理这个事件的生命周期。
对应的 task 提前完成，能取消掉已经生成的 schedule，让他销毁；或者重新设置时间之类的。
时间到后，schedule 对象能 触发 对应的事件（弹窗、发声等），暂时不考虑用户点击弹窗上的按钮，然后自我销毁
并且服务器启动时，要能重新初始化生成 schedule
请你帮我实现一下


先明确 ScheduleTask聚合根对象 的目标：  
- 为 goal、task、reminder 等模块提供一个定时器、任务队列的效果，支持任务重试等的高性能、高可用性的模块。
- 应该支持 cron 字段，能够支持重复循环任务、或者单次任务（本质是特殊的循环任务）
- 应该能够支持可自定义的 payload，或者说是支持payload，但是内部的数据结构不管理，让发起者和接受者自己协调（eg reminder 和 notification）
- 需要和对应发起者联系，能够支持暂停，删除等，eg： reminder 模块生成 template并处于启动状态时，就要生成一个对应 schedule，持久化聚合根对象，根据 reminder 模块发出的事件做出对应服务（reminderTemlate 禁用，则 对应schedule 暂停；reminderTemplate 删除，则对应 schedule 删除 等等）
- 以上的只是以 reminder 模块举例， schedule 应该是一个复用性极强的基础模块，以事件总线的方式为其他模块提供服务
现在请你参照 repository 模块的重构步骤（第一步是先生成 schedule 模块的聚合根、实体、值对象等的类型定义，先明确有哪些聚合根、实体、值对象，他们分别有什么属性、方法!!!先确保这个正确了，在开始后面的内容， 还要参考 repository 的 统计聚合根，也给schedule模块添加一个），重构 schedule 模块，严格参考 repository 模块的相关代码！！！

## Schedule

 Schedule对象 用于日程对象的管理，比如 一个人根据他的 task 和 reminder 和 goal 等，就应该能确定一个人的日程 schedule，然后根据年月日视图来优雅地告诉用户自己的日程，比如日视图可以 展示哪些目标在进行，然后详细的展示 Task，并根据当前的 提醒 也渲染（应该添加一个设置入口来让用户配置是否将 reminder 的也在视图中展示）。

某种程度来讲，这两个其实是可以统一的吧，
他们的日程属性（起始时间、类型task、remidner）都是一样
只不过 scheduleTask 应该是需要在内存中一直运行，来实现到时间立刻触发提醒的事件触发功能，类似底层基础功能；而 schedule 则是把数据用于展示、日程冲突等 schedule 业务功能
