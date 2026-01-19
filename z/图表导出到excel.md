---
tags:
  - status/growing
  - type/howto
  - tech/dev/frontend
description: olp-admin 需要将导出的图表 放在同一个 sheet 中（目前是一个图标一个 sheet），并且我看图表有水平压缩变形。顺便学习记录一下相关技术。
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
keyword:
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[知识库管理体系 MOC]]

---


# 图表导出到excel 


# Excel 图表导出到同一工作表与等比缩放实践

本文记录如何在前端使用 `exceljs` 将多个图表截图导出到同一个 Excel 工作表（Sheet），并避免图表在 Excel 中被压缩变形（特别是水平方向）。

## 目标
- 所有导出的图表集中在一个 Sheet，例如 `Charts`。
- 保持图表原始宽高比例显示，避免被拉伸或压缩。
- 与表格数据（如 Internal / External）分 Sheet 管理，互不影响。

## 关键技术点

- 使用 `exceljs` 的图片插入 API：`worksheet.addImage(imageId, { tl, ext, editAs })`。
  - `tl`: top-left 锚点，使用 `col`/`row`（列/行）定位。
  - `ext`: 以像素为单位的尺寸（width/height）。使用 `ext` 可以避免图片按单元格比例被拉伸。
  - 避免使用 `br`（bottom-right 单元格锚点）来控制图片大小，因为这种方式会将图片尺寸绑定到单元格跨度，容易导致变形。

- 获取 base64 图片的自然尺寸：在浏览器端可通过 `Image` 对象读取 `naturalWidth`/`naturalHeight`。
  - 计算缩放比例：`scale = Math.min(1, maxWidth / naturalWidth)`，再用该比例等比计算目标宽高。

## 代码片段（已集成在业务代码中）

```ts
// 1) 获取 base64 图的尺寸
const getImageSize = (base64: string): Promise<{ width: number; height: number }> =>
  new Promise((resolve, reject) => {
    const img = new Image()
    img.onload = () => resolve({ width: img.naturalWidth || img.width, height: img.naturalHeight || img.height })
    img.onerror = (e) => reject(e)
    img.src = base64
  })

// 2) 统一导出所有图表到同一个 Sheet，并保持等比缩放
const chartsSheet = workbook.addWorksheet('Charts')
const maxWidth = 700
const rowHeightPx = 20
let currentRow = 1

for (const base64 of chartImages) {
  const { width: w, height: h } = await getImageSize(base64)
  const scale = Math.min(1, maxWidth / (w || 1))
  const targetW = Math.round((w || maxWidth) * scale)
  const targetH = Math.round((h || Math.floor(maxWidth * 0.6)) * scale)

  const imageId = workbook.addImage({ base64, extension: 'png' })
  chartsSheet.addImage(imageId, {
    tl: { col: 0, row: currentRow },
    ext: { width: targetW, height: targetH },
    editAs: 'oneCell'
  })
  currentRow += Math.ceil(targetH / rowHeightPx) + 2
}
```

## 常见问题与排查
- 图片仍旧变形：检查是否错误地使用了 `br` 来控制图片大小；应改为 `ext` 像素尺寸。
- 图片重叠：`currentRow` 的推进要足够，或者在不同 `col` 上进行网格化布局（如两列布局）。
- 尺寸不准确：Excel 的行高/列宽与像素存在换算差，`rowHeightPx` 仅用于估算图片纵向间距；图片渲染不会受此影响。

## 建议
- 统一设置 `maxWidth` 控制导出图片的最大宽度，保证文档整体观感一致。
- 若有两列布局需求，可将 `tl.col` 设置为 `0` 与 `12` 两个起点，并在一行内放置两张图（仍使用 `ext` 控制像素尺寸）。

## 参考
- exceljs 文档与源码（图片锚点与尺寸）
- 浏览器端 `Image` API（获取 base64 图片自然尺寸）
