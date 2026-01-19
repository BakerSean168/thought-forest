---
tags: [status/growing, type/concept, tech/dev]
description: base_template
created: 2026-01-16T16:53:25
updated: 2026-01-16T16:54:02
---

# aria 

[ARIA - 无障碍 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/Accessibility/ARIA)

## Overview

**ARIA** 全称是 **Accessible Rich Internet Applications**（无障碍富互联网应用）。

- **它的核心目的**：主要是为了让使用**屏幕阅读器（Screen Readers）**的视障用户能够理解网页的状态。
- **举个例子**：一个仅仅只有图标的按钮 `<button class="icon-btn"></button>`，屏幕阅读器读不出来。但如果你加上 `aria-label="关闭菜单"`，视障用户就能听到了。

### 2. 为什么你在 CSS 中经常看到它？

在过去，我们通常用 `class` 来控制状态，比如：

CSS

```
/* 旧的写法 */
.nav-link.active {
  color: red;
  font-weight: bold;
}
```

而在现代开发（包括 Astro 的最佳实践）中，我们推崇 **"语义化样式"**。既然为了无障碍访问（Accessibility），我们必须给 HTML 加上 `aria-` 属性，那为什么不直接**利用这个属性来写样式**呢？

这就诞生了你看到的写法：

CSS

```
/* 新的写法：利用 ARIA 状态来写样式 */
.nav-link[aria-current="page"] {
  color: red;
  font-weight: bold;
}
```

**这样做的好处是“一石二鸟”：**

1. **无障碍友好**：你被迫必须写好 ARIA 属性，否则样式就不生效。这倒逼开发者写出对屏幕阅读器友好的代码。
2. **状态同步**：不需要维护 `class="is-active"` 和 `aria-expanded="true"` 两套状态，维护一个 ARIA 属性即可。

---

### 3. Astro 中常见的 ARIA 样式场景

在 Astro 项目中，你最可能在以下几种场景见到这种写法：

#### A. 导航栏高亮 (`aria-current`)

这是 Astro 最常用的。当你在不同页面跳转时，Astro 或其路由库会将当前页面的链接标记为 `aria-current="page"`。

HTML

```
<nav>
  <a href="/home">首页</a>
  <a href="/about" aria-current="page">关于我们</a>
</nav>
```

CSS

```
/* CSS 样式 */
a[aria-current="page"] {
  border-bottom: 2px solid blue;
  color: blue;
}
```

#### B. 折叠面板/菜单 (`aria-expanded`)

用于控制下拉菜单或手风琴效果。

HTML

```
<button aria-expanded="true" aria-controls="menu-1">
  菜单
</button>
<div id="menu-1" class="menu-content">...</div>
```

CSS

```
/* CSS：当 expanded 为 true 时，改变箭头的方向 */
button[aria-expanded="true"] .icon-arrow {
  transform: rotate(180deg);
}
```

#### C. 隐藏元素 (`aria-hidden`)

有些装饰性的元素（比如纯装饰用的 SVG 图标），我们不希望屏幕阅读器读出来，同时有时也希望它在视觉上做处理。

CSS

```
/* CSS: 选中所有被标记为对屏幕阅读器隐藏的元素 */
[aria-hidden="true"] {
  /* 有时用来做特定的淡出效果或仅仅是逻辑选中 */
  opacity: 0.5; 
}
```

#### D. 表单错误状态 (`aria-invalid`)

当用户输入错误时，与其加一个 `.error` 类，不如直接用语义化的属性。

HTML

```
<input type="email" aria-invalid="true" />
```

CSS

```
input[aria-invalid="true"] {
  border-color: red;
  background-color: #ffe6e6;
}
```

---

### 4. 总结：这是一种“状态驱动”的思维

你在 Astro 代码库里看到的那些 `[aria-...]` 并不是特殊的 CSS 魔法，而是标准 CSS 选择器。

**对比一下思维模式：**

|**模式**|**做法**|**缺点**|**优点**|
|---|---|---|---|
|**Class 驱动**|`.btn.is-pressed { ... }`|容易忘记加 ARIA 属性，导致视障用户不知道按钮被按下了。|简单直观。|
|**ARIA 驱动**|`.btn[aria-pressed="true"] { ... }`|写起来稍微长一点点。|**样式生效即意味着无障碍支持已就绪**。代码更健壮。|

### 建议

既然你在学习 Astro，我强烈建议你尝试拥抱这种写法。Astro 的哲学就是输出最少、最高效、最语义化的 HTML。

**接下来，你想看一个具体的代码示例吗？** 比如如何用 Astro 实现一个**“完全符合 ARIA 标准且通过属性控制样式的导航栏”**？