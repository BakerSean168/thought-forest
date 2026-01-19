---
tags:
  - status/growing
  - type/debug
  - tech/dev/frontend
description: pagedjs分页边界的DOM不稳定性，导致塌陷问题
project: krjx-ddcc
---



# pagedjs分页边界的DOM不稳定性

当第一列内容为空时，第二（三、四。。）页的首行会错位，往左侧塌陷，并且列宽也不受之前调整的 css 影响。

## 根本原因：pagedjs 分页机制

### 1. **第一页是原始DOM结构**
```
第一页（原始）:
<table class="data-table">
  <colgroup>              ← colgroup 存在
    <col class="col-request-id" />
    ...
  </colgroup>
  <thead>...</thead>
  <tbody>
    <tr>...</tr>          ← 第一行正常
    <tr>...</tr>
  </tbody>
</table>
```
- 第一页使用的是你原始生成的HTML
- `<colgroup>` 完整存在
- 列宽样式（无论是类名还是nth-child）都能正常应用

### 2. **第二页是 pagedjs 重建的结构**
```
第二页（pagedjs分页后）:
<table class="data-table">
  <!-- colgroup 可能丢失或被复制不完整 -->
  <thead>...</thead>       ← 表头可能被重新插入
  <tbody>
    <tr>...</tr>          ← 第一行！这是分页的"接续点"
    <tr>...</tr>
  </tbody>
</table>
```

**关键问题**：
- pagedjs 在分页时会**重新构建**表格DOM
- 第二页的第一行是**分页的接续点**，DOM结构可能不完整
- 如果这一行的某个单元格内容为空（如Request ID），而CSS保护没生效，就会塌陷

### 3. **为什么其他行不塌陷？**

其他行不塌陷的原因：
- 它们有实际内容（Unit、Unit Number等都有值）
- 即使Request ID为空，相邻单元格的内容撑开了整行结构
- 但**第一行作为接续点**，DOM可能处于一个"边界状态"，对空单元格更敏感

## 我们的解决方案

之前用 `colgroup` + 类名：
```css
.data-table .col-request-id { width: 14%; }  ← 依赖colgroup类名
```
**问题**：分页后colgroup丢失 → CSS失效 → 第一行塌陷

现在用 `nth-child` + 强制约束：
```css
/* 直接锁定列，不依赖colgroup */
.data-table td:nth-child(1) { width: 14%; }

/* 防止空单元格塌陷 */
.data-table td {
  min-height: 20px;        ← 强制最小高度
}

.data-table td:empty::after {
  content: '\\200b';        ← 空单元格插入零宽字符
}
```

**为什么这样能解决**：
1. `nth-child` 不依赖DOM属性，直接按顺序选中
2. `min-height` 确保即使内容为空，单元格也有高度
3. `::after` 伪元素确保"技术上不为空"
4. HTML中的 `&nbsp;` 提供额外保险

这个问题的本质是 **pagedjs分页边界的DOM不稳定性**，需要多层防护才能完全避免。