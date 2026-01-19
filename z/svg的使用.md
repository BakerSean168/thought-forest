---
tags:
  - tech/lang/html
  - tech/lang/css
  - type/howto
  - status/growing
description: SVG 可缩放矢量图形使用指南
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[CSS MOC]]

---


# SVG 可缩放矢量图形

## 概念

**名称：** 可缩放矢量图形（Scalable Vector Graphics，SVG）

**特点：**
- 基于 XML 的矢量图形格式
- 无限缩放不失真
- 可被 CSS 样式化和 JavaScript 操作
- SEO 友好，文本可被搜索引擎索引
- 文件体积小（相比位图）

---

## 使用方式

### 1. 内联 SVG

直接在 HTML 中嵌入 SVG 代码：

```html
<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="50" cy="50" r="40" fill="#3498db" />
</svg>
```

### 2. img 标签引用

```html
<img src="icon.svg" alt="图标" width="24" height="24" />
```

### 3. CSS 背景图

```css
.icon {
  background-image: url('icon.svg');
  background-size: contain;
  width: 24px;
  height: 24px;
}
```

### 4. Vue/React 组件化

```vue
<!-- Vue 中使用 vite-svg-loader -->
<template>
  <IconHome class="icon" />
</template>

<script setup>
import IconHome from '@/assets/icons/home.svg?component'
</script>
```

---

## SVG 图标库推荐

| 图标库 | 特点 | 地址 |
|--------|------|------|
| **Lucide** | 简洁美观，Vue/React 支持 | lucide.dev |
| **Heroicons** | Tailwind CSS 官方图标 | heroicons.com |
| **Iconify** | 超大图标集合 | iconify.design |
| **Font Awesome** | 经典图标库 | fontawesome.com |

---

## CSS 操作 SVG

### 修改颜色

```css
svg {
  fill: currentColor; /* 使用当前文字颜色 */
}

svg path {
  fill: #3498db;
  stroke: #2980b9;
  stroke-width: 2;
}
```

### 悬停效果

```css
.icon:hover svg {
  fill: #e74c3c;
  transform: scale(1.1);
  transition: all 0.2s ease;
}
```

---

## Path Data 基础

SVG 的 `<path>` 元素使用 `d` 属性定义路径：

| 命令 | 说明 | 示例 |
|------|------|------|
| M | Move to（移动到） | `M10 10` |
| L | Line to（画线到） | `L50 50` |
| H | Horizontal line（水平线） | `H100` |
| V | Vertical line（垂直线） | `V100` |
| C | Cubic Bezier（三次贝塞尔曲线） | `C...` |
| Z | Close path（闭合路径） | `Z` |

---

## 参考工具

**SVG 编辑器：**
- Lunacy（免费设计工具）
- Affinity Designer
- Inkscape（开源免费）
- Figma/Sketch

**在线工具：**
- SVGOMG（SVG 优化压缩）
- SVG Path Editor（路径编辑）

**Path Data 获取：**
Visual Studio Blend WPF 项目，可以获得完美的 path data，适用于 CSS 的 `clip-path`

---

## 相关笔记

- [[CSS MOC]] - CSS 样式索引
- [[前端基础 MOC]] - 前端基础知识
