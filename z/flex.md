---
tags:
  - tech/lang/css
  - type/concept
  - status/seed
description: CSS Flexbox弹性布局
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[CSS MOC]]

---


### flex

```css
display: flex;

/* 主轴方向 */
  flex-direction: row;            /* 默认值：从左到右 */
  /* flex-direction: row-reverse; */ /* 从右到左 */
  /* flex-direction: column; */      /* 从上到下 */
  /* flex-direction: column-reverse; */ /* 从下到上 */

/* 换行方式 */
flex-wrap: nowrap;

/* 主轴对齐方式 */
jusity-content: flex-start;

/* 交叉轴对齐方式 */
align-items: stretch;

/* 多行对齐方式 */
align-content: stretch;

```

