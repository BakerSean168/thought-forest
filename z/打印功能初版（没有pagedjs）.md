---
tags:
  - tech/dev/frame
  - type/howto
  - status/growing
description: Vue打印功能实现-签名表重复、自动分页原理
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[前端问题汇总]]

---


# 打印功能实现原理详解

## 概述

本文档详细分析当前打印功能的实现原理，重点解释：
1. 签名表如何在每页底部重复显示
2. 数据表如何实现自动分页
3. 是否使用了 pagedjs 库
4. 核心技术实现细节

---

## 一、技术栈和实现方案

### 1.1 使用的技术

| 技术 | 用途 | 是否使用 |
|------|------|---------|
| **CSS Paged Media** | 通过 `@page`、`position: fixed` 等实现分页 | ✅ 是 |
| **vue-print-next** | 提供打印预览和打印触发功能 | ✅ 是 |
| **pagedjs** | 复杂分页库（polyfill） | ❌ **否** |
| **原生 CSS 打印媒体查询** | `@media print` 控制打印样式 | ✅ 是 |

**关键结论：当前实现完全基于 CSS Paged Media 规范，没有使用 pagedjs 库。**

### 1.2 为什么不用 pagedjs？

之前的代码尝试引入过 pagedjs：

```typescript
// 旧代码（已移除）
const pagedPolyfillUrl = new URL(
  '../../../node_modules/pagedjs/dist/paged.polyfill.js',
  import.meta.url
).href

export const getPagedExtraHead = (additionalHead = '') =>
  `<script src="${pagedPolyfillUrl}"></script>${resetPrintStyle}${additionalHead}`
```

**移除原因：**
1. `<script>` 标签在 `vue-print-next` 的 `extraHead` 中可能无法正确加载
2. pagedjs 增加了额外的依赖和复杂度
3. CSS 原生功能已经足够满足需求
4. 避免脚本加载失败导致打印预览空白

---

## 二、签名表每页底部重复的实现原理

### 2.1 核心技术：`position: fixed` in `@media print`

签名表固定在每页底部是通过 CSS 的 **`position: fixed`** 在打印上下文中实现的。

#### 关键代码（paged.ts）

```css
/* 打印时优化 */
@media print {
  /* 确保签名表在页面底部 */
  .signature-section {
    position: fixed;        /* 关键：固定定位 */
    bottom: 0;              /* 固定在底部 */
    left: 0;
    right: 0;
    background: white;      /* 白色背景防止透明 */
    padding-top: 15mm;      /* 上方留白，避免覆盖数据 */
  }
}
```

### 2.2 工作原理详解

#### 在打印上下文中，`position: fixed` 的特殊行为：

```
┌─────────────────────────────────────┐
│         Page 1                      │
│  ┌─────────────────────────────┐   │
│  │ 表头（MAJNOON IFMS）        │   │
│  └─────────────────────────────┘   │
│  ┌─────────────────────────────┐   │
│  │ 数据表格                    │   │
│  │ Row 1                       │   │
│  │ Row 2                       │   │
│  │ ...（自动填充）              │   │
│  └─────────────────────────────┘   │
│                                     │
│  ↓ 剩余空间 ↓                      │
│                                     │
│  ┌─────────────────────────────┐   │
│  │ 签名表（fixed bottom）      │ ← 固定在此
│  │ Applicant | Line Manager    │   │
│  │ GRM       | BOC             │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│         Page 2                      │
│  ┌─────────────────────────────┐   │
│  │ 数据表格（续）               │   │
│  │ Row N+1                     │   │
│  │ Row N+2                     │   │
│  │ ...                         │   │
│  └─────────────────────────────┘   │
│                                     │
│  ┌─────────────────────────────┐   │
│  │ 签名表（fixed bottom）      │ ← 每页都重复
│  │ Applicant | Line Manager    │   │
│  │ GRM       | BOC             │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

#### 关键机制：

1. **`position: fixed`** 在打印模式下，相对于**每一页的打印区域**定位，而不是整个文档
2. **`bottom: 0`** 确保签名表始终在每页底部
3. **浏览器自动处理** 分页时，会在每一页都渲染这个固定定位的元素

### 2.3 为什么需要 `padding-top: 15mm`？

```css
.signature-section {
  padding-top: 15mm;  /* 关键：上方留白 */
}
```

**原因：** `position: fixed` 的元素会覆盖在其他内容之上，如果不留白，签名表会遮挡数据表格的最后几行。

**解决方案：**

```css
.data-table {
  margin-bottom: 140mm;  /* 为签名表预留足够空间 */
}
```

这样数据表格的底部不会延伸到签名表覆盖的区域。

---

## 三、数据表自动分页的实现原理

### 3.1 核心技术：CSS 分页控制属性

数据表的自动分页完全依靠 **CSS Paged Media** 规范，浏览器自动计算每页能容纳多少行。

#### 关键代码（paged.ts）

```css
/* A4 纸张设置 */
@page {
  size: A4 portrait;      /* 纸张大小和方向 */
  margin: 15mm 10mm;      /* 页边距 */
}

/* 防止表格行跨页断裂 */
.data-table tbody tr {
  break-inside: avoid;          /* CSS 规范 */
  page-break-inside: avoid;     /* 兼容旧浏览器 */
}
```

### 3.2 自动分页的工作流程

```
┌──────────────────────────────────────────────────────────┐
│  1. 浏览器读取 @page 配置                                │
│     - 纸张: A4 (210mm × 297mm)                           │
│     - 页边距: 上下15mm, 左右10mm                         │
│     - 可用高度: 297mm - 30mm = 267mm                     │
│     - 可用宽度: 210mm - 20mm = 190mm                     │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  2. 计算固定元素占用空间                                 │
│     - 表头 (page-header): ~120mm                        │
│     - 签名表 (signature-section): ~140mm                │
│     - 剩余数据区域: 267mm - 120mm - 140mm = 7mm (错!)   │
│                                                          │
│  实际上数据表有 margin-bottom: 140mm，留给签名表空间     │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  3. 浏览器自动填充数据行                                 │
│     - 每行高度: ~10mm (包括 padding 和 border)          │
│     - 每页可容纳行数: (267mm - 120mm) / 10mm ≈ 14行      │
│     - 遇到 break-inside: avoid 时，整行移到下一页        │
└──────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────┐
│  4. 分页边界处理                                         │
│     - 如果一行会跨页断裂 → 整行移到下一页                │
│     - 固定元素（签名表）在每页重复显示                   │
│     - 继续填充下一页数据                                 │
└──────────────────────────────────────────────────────────┘
```

### 3.3 为什么不需要手动计算分页？

#### 对比：手动分页 vs CSS 自动分页

**手动分页方案（未采用）：**

```typescript
// ❌ 不推荐：需要硬编码每页行数
const ROWS_PER_PAGE = 8
const pages = []
for (let i = 0; i < data.length; i += ROWS_PER_PAGE) {
  pages.push(data.slice(i, i + ROWS_PER_PAGE))
}

// 为每页生成独立的 HTML
pages.forEach((pageData, index) => {
  html += `
    <div class="page">
      ${headerHTML}
      <table>${generateRows(pageData)}</table>
      ${signatureHTML}
    </div>
  `
})
```

**问题：**
- 行数固定，不适应内容高度变化
- 如果表头、签名表高度改变，需要重新计算
- 代码复杂，维护困难

**CSS 自动分页方案（当前采用）：✅**

```typescript
// ✅ 推荐：浏览器自动计算
const dataRows = data.map(item => `<tr>...</tr>`).join('')

return `
  <div class="print-container">
    <div class="page-header">...</div>
    <table class="data-table">
      <tbody>${dataRows}</tbody>
    </table>
    <div class="signature-section">...</div>
  </div>
`
```

**优点：**
- 浏览器根据纸张大小自动计算每页行数
- 适应不同的内容高度和字体大小
- 代码简洁，易于维护
- 完全遵循 CSS 标准

---

## 四、HTML 结构设计

### 4.1 完整的 HTML 结构

```html
<div class="print-container">
  
  <!-- 第一页的表头（仅第一页显示）-->
  <div class="title-section">
    <div class="page-header">
      <div class="print-header">
        <div class="logo-cell"><img src="majnoon.png" /></div>
        <div class="title-cell">MAJNOON IFMS PROJECT</div>
        <div class="logo-cell"><img src="anton.png" /></div>
      </div>
      <div class="document-title">
        HC Document and Records<br/>
        Retrieval Register
      </div>
    </div>
  </div>
  
  <!-- 数据表格区域 -->
  <div class="data-section">
    <table class="data-table">
      <thead>
        <tr>
          <th>Request ID</th>
          <th>Unit</th>
          <th>Unit Number</th>
          <!-- ... -->
        </tr>
      </thead>
      <tbody>
        <tr class="data-row">...</tr>
        <tr class="data-row">...</tr>
        <!-- 浏览器自动分页 -->
        <tr class="data-row">...</tr>
        <!-- ... 更多行 ... -->
      </tbody>
    </table>
  </div>
  
  <!-- 签名表（每页底部重复）-->
  <div class="signature-section">
    <table class="signature-table">
      <tr>
        <td>Applicant</td>
        <td>Approved by(Line Manager)</td>
      </tr>
    </table>
    <table class="signature-table">
      <tr>
        <td>Requested by(GRM)</td>
        <td>Approved by(BOC)</td>
      </tr>
    </table>
    <div class="comments-box">Comments-</div>
  </div>
  
</div>
```

### 4.2 为什么表头不在每页重复？

当前实现中，LOGO 和标题只在第一页显示。如果需要每页重复，可以使用：

```css
.page-header {
  display: table-header-group;  /* 未启用 */
}
```

但这需要 `<table>` 结构，当前是 `<div>` 结构，所以表头不重复。

**如果需要表头每页重复，有两种方案：**

1. **将整个内容改为 table 结构**（复杂）
2. **使用 running header**（需要 pagedjs 或高级 CSS 支持）

---

## 五、CSS 分页控制属性详解

### 5.1 核心属性表

| 属性 | 作用 | 使用位置 |
|------|------|---------|
| `@page` | 定义纸张大小、页边距 | 全局 |
| `break-inside: avoid` | 防止元素内部分页断裂 | 表格行 |
| `page-break-inside: avoid` | 同上（兼容性） | 表格行 |
| `position: fixed` | 固定定位（打印时每页重复） | 签名表 |
| `display: table-header-group` | 表头每页重复（未使用） | - |

### 5.2 浏览器兼容性

```
✅ Chrome/Edge (Chromium): 完全支持
✅ Firefox: 完全支持
✅ Safari: 支持（可能有细微差异）
⚠️ 旧版 IE: 不支持（已淘汰）
```

---

## 六、关键技术难点和解决方案

### 6.1 难点 1: 签名表覆盖数据

**问题：** `position: fixed` 会覆盖数据表格的最后几行

**解决方案：**

```css
.data-table {
  margin-bottom: 140mm;  /* 为签名表预留空间 */
}

.signature-section {
  padding-top: 15mm;     /* 签名表上方留白 */
}
```

### 6.2 难点 2: 预览和打印宽度不一致

**问题：** 预览时占满，打印时右侧留白

**原因：** `@page margin` 导致可用宽度减少

**解决方案：**

```css
/* 预览模式 */
@media screen {
  .print-container {
    max-width: 210mm;
    padding: 15mm 10mm;  /* 模拟页边距 */
  }
}

/* 打印模式 */
@media print {
  .print-container {
    width: 100%;  /* 自适应可用宽度 */
    padding: 0;
  }
}
```

### 6.3 难点 3: 表格行跨页断裂

**问题：** 表格行在分页边界被截断

**解决方案：**

```css
.data-table tbody tr {
  break-inside: avoid;
  page-break-inside: avoid;
}
```

浏览器会确保整行移到下一页。

---

## 七、性能和优化

### 7.1 为什么不用 JavaScript 手动分页？

| 方面 | CSS 自动分页 | JS 手动分页 |
|------|-------------|------------|
| **性能** | 浏览器原生，速度快 | 需要计算，可能卡顿 |
| **准确性** | 浏览器精确计算 | 容易出错 |
| **适配性** | 自适应纸张大小 | 需要硬编码 |
| **维护性** | 修改 CSS 即可 | 修改逻辑复杂 |

### 7.2 HTML 生成优化

```typescript
// 一次性生成所有行，避免多次字符串拼接
const dataRows = data.map(item => `
  <tr class="data-row">
    <td>${item?.applyCode || ''}</td>
    <!-- ... -->
  </tr>
`).join('')  // 使用 join('') 比 += 更高效
```

---

## 八、总结

### 8.1 核心技术栈

```
┌─────────────────────────────────────────┐
│         打印功能技术架构                │
├─────────────────────────────────────────┤
│  1. vue-print-next                      │
│     └─ 提供打印预览和触发功能           │
│                                         │
│  2. CSS Paged Media (@page, position)  │
│     └─ 实现自动分页和签名表重复         │
│                                         │
│  3. 原生浏览器打印 API                  │
│     └─ window.print()                   │
│                                         │
│  4. CSS Media Queries (@media print)   │
│     └─ 区分预览和打印样式               │
└─────────────────────────────────────────┘

❌ 不使用: pagedjs, html2canvas, jsPDF
```

### 8.2 实现要点

1. **签名表每页重复**
   - 核心：`position: fixed` + `bottom: 0` + `@media print`
   - 浏览器在每页都渲染固定定位元素
   - 需要预留空间避免覆盖数据

2. **数据表自动分页**
   - 核心：`@page` + `break-inside: avoid`
   - 浏览器根据纸张大小自动计算每页行数
   - 无需手动 JavaScript 计算

3. **没有使用 pagedjs**
   - 原生 CSS 功能已足够
   - 避免额外依赖和复杂度
   - 更好的性能和兼容性

### 8.3 代码位置

```
src/components/Print/
├── paged.ts                 # CSS 样式定义（核心）
├── composables/
│   └── useRetrievalPrint.ts # HTML 生成逻辑
└── components/
    └── PrintButton.vue      # 打印触发组件
```

---

## 九、扩展阅读

### 9.1 CSS Paged Media 规范

- [W3C CSS Paged Media Module](https://www.w3.org/TR/css-page-3/)
- [MDN @page](https://developer.mozilla.org/en-US/docs/Web/CSS/@page)
- [MDN page-break-inside](https://developer.mozilla.org/en-US/docs/Web/CSS/page-break-inside)

### 9.2 相关技术对比

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|---------|
| CSS Paged Media | 简单、标准、性能好 | 浏览器支持差异 | 简单打印需求 ✅ |
| pagedjs | 功能强大、polyfill | 依赖大、复杂 | 复杂排版 |
| html2canvas + jsPDF | 精确控制 | 性能差、体积大 | 需要 PDF 导出 |

**当前选择：CSS Paged Media** - 最适合当前需求，简单高效。

