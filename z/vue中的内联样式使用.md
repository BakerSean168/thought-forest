---
tags:
  - tech/dev/frontend
  - type/concept
  - status/seed
description: vue中的内联样式使用
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---


# 静态

- 普通的 HTML 属性
- 值是字符串

`style="background-color: rgb(var(--v-theme-background));">`

# 动态

- Vue 的指令，`v-bind:style` 的简写
- 值是 JavaScript 表达式
- 支持响应式数据、计算属性

```Vue
:style="{ backgroundColor: 'rgb(var(--v-theme-background))' }">
<!-- 对象语法 -->
:style="{ color: textColor, fontSize: fontSize + 'px' }"

<!-- 数组语法 -->
:style="[baseStyles, overridingStyles]"

<!-- 使用计算属性 -->
:style="computedStyles"

<!-- CSS 变量 -->
:style="{ '--my-color': themeColor }"
```

# 高级用法

结合选择表达式
:style="selectValue === tab.value ? { color: red } : {}"
