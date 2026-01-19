---
tags:
  - tech/dev/pm
  - type/howto
  - status/seed
description: Vue项目提醒模块设计
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DailyUse-MOC]]

---


## 准备

### Reminder 模块的核心任务是：
一个独立的主要用于循环重复提醒的功能模块
1. 支持启动暂停，支持小组式批量启动暂停管理的循环提醒（每隔xx分或者每天9：00）

### 提醒任务组

提醒任务组，将多个同类型的提醒任务归为一组，能够管理多个提醒任务，也可以达到组合提醒的效果。

### 提醒任务

提醒周期：

能够选择一个时间段（或一直持续），在时间段内重复 提醒任务  
可以添加专注功能（专注周期），屏蔽其他普通提醒（但保留重要提醒）

任务周期：

完成一次定义的所有任务所需要的时间

任务时间：

应该支持复杂的时间设置：

- 经过 40m 后提醒，休息 10m；
- 经过随机 3-5m 后提醒，休息 30s；持续 90m 后，休息 10m；
  整个任务周期为 100m，但是任务周期里有多个小周期 3.5m-5.5m，默认的提醒周期应该为一个任务周期，但应该可以被用户设置

```ts
{
  name: "专注训练",
  description："专注90m，休息10m",
  duration: 100,
  type: 'relative',
  times: [
    {
      name: "专注周期",
      description: "多个小周期专注3-5m，休息30s",
      duration: 90,
      times: [
        {
            name: "专注小周期",
            duration: 3.5-5.5,
        },
        {
          name: "休息小周期",
          duration: 30,
        }
      ]
    }，
    {
      name: "休息周期",
      duration: 10,
    }
  ],
}
```

### 可视化功能：

- 添加一个任务周期
- 添加一个提醒周期

基础提醒任务组：

- 起床提醒：绝对时间 8:00
- 饭点提醒：多个绝对时间点
- 睡觉提醒：绝对时间 23:00

电脑前任务组：

- 休息提醒：每过 40m 提醒一次 休息 5m

# 渲染进程

## presentation

实现页面是一个左右布局，  
左侧是主要内容区域，以网格布局形式，展示 items（group、template）。点击item，展示粗略信息，顶部右上角显示 selfEnable 状态控制组件（switch），和实际 status 状态。点击 group，同样顶部 名称 + 控制模式 + 状态 组件，下方则是小型 网格布局，以网格形式展示 group 下的 group。使用 右键菜单来针对不同目标显示不同的 修改、增加、删除等功能。  
右侧是一些数据统计，比如即将到来的 reminder。右侧应该是 可以收起或打开的。

### 实现细节

#### reminderGroup 的模式更改时页面响应式变化的问题

传入 reminderGroupCard 的对象是通过下面的响应式对象传入的，传入后在 Card 中调用了方法，通过ipc修改对象的 enableMode，并在 store 中同步，但是 Card 中还没有响应式更新 enableMode的状态。该怎么做  
```ts
const reminderTemplateGroupCard = ref({
  show: false,
  templateGroup: null as ReminderTemplateGroup | null
});
```

有两种方案：  
1. 传入 templateGroupUuid 而非完整对象，通过传入的 uuid 获取 store 中的对象，绕过了 Card 传入的对象不是响应式的问题。
2. 传入 完整对象，但是页面中渲染的属性（如 enbaled）还是通过 computed 和 传入对象的 uuid 从 store 中获取。

好像其实是一个方案

## template 启用状态的逻辑

受到 group 的 启用模式、是否启用 和 reminderTemplate 的 自我启用状态 的影响
- group 启用模式 为 group（受组控制）时， template 的 enable = group.enable
- group.enableMode = individual 时，template.enable = template.selfEnable

## 桌面作为根Group（特殊group）

这样让每个 reminderTemplate 都有所属 group，
方便控制渲染逻辑，所有 reminder 都只渲染一次，根据 groupUuid 来渲染
复用 group 的控制逻辑 来 作为 控制所有 reminder 的方法（好像不行，目前没有实现 Group 的启用状态计算业务，也没必要实现；还是额外加一个启用或者停止所有 reminder 的方法吧）
