---
tags:
  - tech/dev/frame
  - type/debug
  - status/growing
description: Vue/Vuetify对话框中时间组件样式与状态同步问题解决
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[Vuetify MOC]]

---


## 时间组件样式问题

具体问题：  
现在当开关时间相关的按钮，让时间消失又出现后，它的样式会变得没有时间、但本身的值还在，怎么让他再次显示时，能立刻更新样式，显示时间

原因：  
当时间字段被隐藏和重新显示时，Vue/Vuetify的输入框组件没有正确地重新渲染其显示状态。这通常发生在v-show或条件渲染中。？？
组件的值和数据的值并不相同
- 组件需要 YYYY-MM-DD 的 string  
- 绑定的数据却是 一个复杂的自定义的时间对象

方法：  
将时间组件与一个 ref 对象（xxxInput）绑定，以及利用 `@update` 钩子 调用的 实现一个利用该对象来更新自定义数据的函数（handlexxx update）  
