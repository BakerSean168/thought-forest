---
tags:
  - tech/dev/frame
  - type/howto
  - status/growing
description: Element Plus表格合并单元格实现方法
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[Element Plus MOC]]

---


# element表格合并单元格详解 

## 🎯 Element‑Plus 表格合并单元格（`span-method`）概览

在 **Element‑Plus** 中，表格的单元格合并是通过 `el-table` 的 `:span-method` 属性实现的。
它接受一个 **函数**，在渲染每个单元格时被调用，返回 **行跨度**（`rowspan`）和 **列跨度**（`colspan`），从而决定该单元格是否需要跨行/跨列。

| 返回形式 | 说明 | 适用场景 |
|----------|------|----------|
| **数组** `([rowspan, colspan])` | 简洁写法，常用于 **固定的合并规则**（如每两行合并一次） | 示例代码中的 `arraySpanMethod` |
| **对象** `{ rowspan: number, colspan: number }` | 更直观，可在同一函数里对不同列返回不同合并方式 | 示例代码中的 `objectSpanMethod` |

> **关键点**：如果返回 `0`（`rowspan: 0` 或 `colspan: 0`），该单元格会被 **隐藏**，因为它已被前面的单元格“占位”。

下面我们逐步拆解官方实现的核心概念，并结合你提供的代码进行说明。

---

## 1️⃣ `span-method` 的函数签名

```ts
type SpanMethod = (props: {
row: T;// 当前行对应的数据对象
column: TableColumnCtx<T>; // 当前列的配置信息（列名、宽度等）
rowIndex: number;// 当前行的索引（从 0 开始）
columnIndex: number; // 当前列的索引（从 0 开始）
}) => number[] | { rowspan: number; colspan: number } | void;
```

- **`row` / `column`**：如果你想基于某列的值来决定是否合并，可直接读取 `row[column.property]`。
- **`rowIndex` / `columnIndex`**：常用来实现“每 N 行合并一次”或“只在特定列合并”。

---

## 2️⃣ 代码实例解读

### 2.1 `arraySpanMethod`（返回数组）

```ts
const arraySpanMethod = ({
rowIndex,
columnIndex,
}: SpanMethodProps) => {
// 只在偶数行（0、2、4…）进行合并
if (rowIndex % 2 === 0) {
if (columnIndex === 0) {// 第 1 列（ID）
return [1, 2]// rowspan = 1, colspan = 2
} else if (columnIndex === 1) {// 第 2 列（Name）
return [0, 0]// 隐藏该单元格，已被前面的跨列占位
}
}
}
```

- **效果**：在 **ID** 列的偶数行上，`ID` 与 **Name** 两列合并为一个宽度为两列的单元格；随后 **Name** 列的对应单元格被隐藏（`[0,0]`），避免重复渲染。
- **为什么返回 `[1,2]` 而不是 `[2,1]`？**
- `rowspan` 为 **1**（不跨行），`colspan` 为 **2**（跨两列）。

### 2.2 `objectSpanMethod`（返回对象）

```ts
const objectSpanMethod = ({
rowIndex,
columnIndex,
}: SpanMethodProps) => {
if (Index === 0) {// 只在第一列（ID）处理
if (rowIndex % 2 === 0) {
return { rowspan: 2, colspan: 1 } // 合并当前行与下一行
} else {
return { rowspan: 0, colspan: 0 } // 隐藏被占位的单元格
}
}
}
```

- **效果**：在 **ID** 列的偶数行上，向下跨 **2 行**（即与下一行合并），列宽不变。
- **隐藏规则**：奇数行（被合并的那一行）返回 `rowspan:0`，让它不再渲染。

> **小技巧**：如果你只想在某几列合并，直接在 `if (columnIndex === X)` 中返回对应的对象/数组；其他列返回 `undefined`（即不合并），即可保持默认渲染。

---

## 3️⃣ 常见合并需求与实现方式

| 需求 | 示例实现 | 说明 |
|------|----------|------|
| **每 N 行合并一次**（如每 3 行） | ```ts if (rowIndex % 3 === 0 && columnIndex === 0) return [3,1]; ``` | `rowspan` 为 3，跨 3 行；后两行返回 `{rowspan:0}` 隐藏。 |
| **同一列值相同则合并** | ```ts if (rowIndex > 0 && row.id === tableData[rowIndex-1].id) return {rowspan:0}; else { // 计算连续相同的数量 }``` | 需要遍历后续行，统计相同值的连续长度。 |
| **跨列合并（标题行）** | 在 `<el-table>` 上方自行写 `<tr><th colspan="5">标题</th></tr>`，或在 `span-method` 中对第一行返回 `[1,5]`。 | 跨列合并常用于表头或汇总行。 |
| **动态根据数据决定合并** | 使用 `row.amount1 > 200 ? [1,2] : undefined` | 直接在函数里读取行数据进行判断。 |

> **注意**：`span-method` 在 **每次渲染** 都会被调用，若返回值依赖于遍历后面的行，需要 **缓存** 计算结果，以免出现不一致的合并效果。

---

## 4️⃣ 完整示例（同列值相同合并）

下面给出一个 **完整的 Vue3 + Element‑Plus** 示例，演示 **当 `name` 相同且相邻时合并该列**：

```vue
<template>
<el-table :data="tableData" :span-method="nameSpanMethod" border style="width: 100%">
<el-table-column prop="id" label="ID" width="180" />
<el-table-column prop="name" label="Name" />
<el-table-column prop="amount1" label="Amount 1" />
</el-table>
</template>

<script lang="ts" setup>
import type { TableColumnCtx } from 'element-plus'

interface User {
id: string
name: string
amount1: string
}

// 计算每行 name 合并的行数（缓存）
const nameRowSpan = new Map<number, number>()
for (let i = 0; i < tableData.length; ) {
let j = i + 1
while (j < tableData.length && tableData[j].name === tableData[i].name) j++
const span = j - i // 连续相同的数量
nameRowSpan.set(i, span) // 只在首行记录
for (let k = i + 1; k < j; k++) nameRowSpan.set(k, 0) // 其行设 0
i = j
}

const nameSpanMethod = ({
row,
column,
rowIndex,
columnIndex,
}: {
row: User
column: TableColumnCtx<User>
rowIndex: number
columnIndex: number
}) => {
if (column.property === 'name') {
const rowspan = nameRowSpan.get(rowIndex) ?? 1
return { rowspan, colspan: 1 }
}
}
</script>
```

- **核心思路**：先遍历一次 `tableData`，把每个 `name` 连续出现的行数记录在 `nameRowSpan` 中；在 `span-method` 里直接读取对应的 `rowspan`。
- **优势**：避免在每次渲染时重复计算，提高性能。

---

## 5️⃣ 常见坑点 & 调试技巧

| 坑点 | 现象 | 解决办法 |
|------|------|----------|
| **返回值不统一** | 有时返回数组，有时返回对象，导致渲染异常 | 统一返回同一种结构（推荐对象），或确保所有分支都有返回。 |
| **跨行合并后出现空白行** | `rowspan` 大于实际行数 | 计算 `rowspan` 时要判断是否越界（`rowIndex + rowspan <= data.length`）。 |
| **列索引变化导致合并错位** | 动态增删列后 `columnIndex` 与预期不符 | 使用 `column.property` 而非 `columnIndex` 判断列。 |
| **性能问题** | 大表格每次渲染都遍历全表 | 将合并计算提前（如上例的缓存）或使用 `computed` 记忆化。 |
| **样式冲突** | 合并单元格后边框不完整 | 给表格加 `border`，或自行在 CSS 中补全 `border-collapse: collapse;`。 |

> **调试技巧**：在 `span-method` 中 `console.log({rowIndex, columnIndex, returnValue})`，观察每个单元格的返回值是否符合预期。

---

## ✅ 小结

1. `span-method` 用来控制 **行/列跨越**，返回 `[rowspan, colspan]` 或 `{rowspan, colspan}`。
2. 通过 `rowIndex`、`columnIndex`（或 `column.property`）可以精准定位需要合并的单元格。
3. **返回 `0`** 表示该单元格被前面的跨行/跨列占位，需要隐藏。
4. 为避免重复计算，**提前缓存** 合并信息是最佳实践。
5. 常见需求（每 N 行、相同值合并、跨列标题）都可以在 `span-method` 中实现，只要返回正确的跨度即可。


