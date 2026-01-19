---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: AG-Grid
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[前端基础 MOC]]

---


# AG Grid 使用手册

本文档总结了在本项目中使用 AG Grid 的基础概念、使用指南、实战经验、经验总结与参考资料，目标是帮助开发者快速理解并正确实践“虚拟表格 + 动态合并单元格”场景。

---

## 一、基础概念

- AG Grid：一个企业级的网格（data grid）组件，支持 Vue / React / Angular / Plain JS。功能包括虚拟渲染（高性能）、列/行分组、行/列合并（row spanning / cell spanning）、服务器端模型、排序、筛选、聚合、可编辑单元格等。
- 虚拟渲染（Virtualization）：只渲染视口内可见的 DOM 节点，滚动时增量渲染/重用节点，从而支持十万级数据行的流畅展示。
- Row Spanning（行合并）：AG Grid 将单元格抽象为可以跨多个可见行的单元格（通过 `enableCellSpan` + `colDef.spanRows` 或 `spanRows: true` 实现）。与传统 `<table>` 的 `rowspan` 不同，AG Grid 在它的渲染层面通过 CSS/DOM 控制合并行为（更灵活，能和虚拟渲染配合）。
- 模块化：AG Grid 从 v24 开始使用模块化体系（community modules / enterprise modules）。使用前必须 `ModuleRegistry.registerModules(...)` 注册所需模块，或者注册 `AllCommunityModule` 以快速启用社区版全部功能。

---

## 二、使用指南（Vue 3 + ag-grid-vue3）

### 1) 安装

使用 pnpm/npm/yarn 安装：

```bash
pnpm add ag-grid-vue3 ag-grid-community
# 或者： npm i ag-grid-vue3 ag-grid-community
```

### 2) 注册模块（必须）

在使用组件之前注册模块（推荐全部社区模块）：

```ts
// 在组件文件或全局入口中注册一次
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community'
ModuleRegistry.registerModules([AllCommunityModule])
```

### 3) 最小示例（组件内）

```vue
<template>
  <div class="ag-theme-alpine" style="height:520px; width:100%">
    <AgGridVue
      :columnDefs="columnDefs"
      :rowData="rowData"
      :defaultColDef="defaultColDef"
      :enableCellSpan="true"
      :getRowId="getRowId"
      @grid-ready="onGridReady"
    />
  </div>
</template>

<script setup lang="ts">
import { AgGridVue } from 'ag-grid-vue3'
import { ref } from 'vue'

const columnDefs = ref([
  { field: 'skill', spanRows: true },
  { field: 'contentTitle', tooltipField: 'contentTitle' }
])
const defaultColDef = { resizable: true }
const rowData = ref([])

const getRowId = (params: any) => params.data.uniqueKey
const onGridReady = (e: any) => {/* 保存 api 等 */}
</script>
```

### 4) 启用合并（示例说明）

- 在 `columnDefs` 中对需要合并的列加 `spanRows: true`。
- 全局启用 `enableCellSpan=true`。
- AG Grid 在可见区域内自动计算合并范围（滚动、排序、筛选时会动态重算）。
- `getRowId` 用于行标识（当你生成 `uniqueKey` 避免重复 ID 时非常重要）。

### 5) 虚拟滚动与服务器端分页

- AG Grid 默认已经实现 DOM 虚拟化（视图虚拟化）。
- 若使用服务器端分页/虚拟化，请使用 AG Grid 的 Server-Side Row Model 或 Infinite Row Model（按需加载）：
  - `rowModelType: 'infinite'` 或 `rowModelType: 'serverSide'`。
  - 提供 datasource，AG Grid 会在滚动接近底部时请求新页数据。

---

## 三、实战经验（项目中遇到的关键点）

1. 数据唯一键（`getRowId`）
   - 如果后端 contentId 存在重复（例如一个 content 在多个 skill 下出现），需要构造 `uniqueKey`（例如 `${skillId}-${contentId}`），并在 `getRowId` 返回它，避免 AG Grid 的 "No unique row id" 或行 key 冲突。

2. 模块注册错误
   - 常见报错：`No AG Grid modules are registered!`。解决方法是确保在使用 `AgGridVue` 之前执行 `ModuleRegistry.registerModules(...)`。

3. 与 CSS 的互动
   - AG Grid 使用自己的单元格 DOM（`div.ag-cell`），可以通过 `.ag-cell` / `.ag-header-cell` 调整对齐、行高、字体等（示例项目已把 cell 设置为 `display:flex; align-items:center` 以实现垂直居中）。

4. Row Spanning 与虚拟渲染
   - AG Grid 能动态计算合并并与虚拟渲染协同工作，这一点是它优于基于 `<table>` 的表格库（Element Plus 等）的关键所在。

5. Tooltip 与文本截断
   - 使用 `tooltipField` 或 `tooltipValueGetter` 来显示完整文本（即使单元格被 `text-overflow: ellipsis` 截断）。

6. 性能建议
   - 尽量在后端做过滤/分页/排序，减少前端数据量。
   - 使用 `rowModelType: 'serverSide'` 可在非常大数据量下保持流畅。
   - 减少复杂 cell renderer（尽量用简单的 DOM/模板），复杂渲染放到懒渲染或外部弹窗。

---

## 四、经验总结（要点）

- AG Grid 适合需要高性能、复杂表格交互（大数据、行/列合并、服务器端模型）的场景。
- 如果你的合并需求仅是静态或每 N 行固定合并，轻量级组件（Element Plus 的 table）可能更简单；但如果要求与虚拟滚动/增量渲染配合，AG Grid 是更稳健的选择。
- 集成注意事项：模块注册、唯一行 ID、尽量把重逻辑放服务器端。

---

## 五、信息参考

- AG Grid 官方文档（Row Spanning）: https://www.ag-grid.com/vue-data-grid/row-spanning/
- AG Grid Vue 介绍: https://www.ag-grid.com/vue-data-grid/
- AG Grid 社区模块注册: https://www.ag-grid.com/javascript-data-grid/modules/
- 项目中实现要点示例：`src/views/edp/learningcontentsnapshot/LearningContentTable.vue`

---

## 六、Appendix：快速上手命令（项目内）

在本项目中（已使用 pnpm）：

```powershell
pnpm add ag-grid-vue3 ag-grid-community
# 启动开发服务器
pnpm dev
```

---

如果你希望我把这份文档放到其他位置、转换成中文/英文双语或生成一个 README 片段（用于 PR 描述），我可以继续帮你调整。
