---
tags:
  - tech/dev/library
  - type/howto
  - status/growing
description: Tiptap 富文本编辑器 Vue3 集成
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[JavaScript 工具库 MOC]] | [[Vue3 MOC]] | [[前端组件 MOC]]

---

# tiptap-Vue3

## 🚀 Tiptap 在 Vue 中的使用详解

### 1\. 核心集成包

在 Vue 中使用 Tiptap，主要依赖以下两个包：

1.  `@tiptap/vue-3` (或 `@tiptap/vue-2`): 提供了核心的 Vue 组合式 API (`useEditor`) 和组件 (`EditorContent`)。
2.  `@tiptap/pm`: ProseMirror 核心包，所有 Tiptap 依赖的基础。

### 2\. 安装步骤

安装基础的 Vue 3 组件和常用的功能扩展包：

```bash
# 1. 安装 Vue 3 集成层和 ProseMirror 核心
npm install @tiptap/vue-3 @tiptap/pm

# 2. 安装常用功能扩展（推荐使用 StarterKit）
npm install @tiptap/starter-kit
```

### 3\. 基本使用（Composition API - `useEditor`）

在 Vue 3 中，推荐使用 `setup` 语法和组合式 API (`useEditor`) 来创建和管理编辑器实例。

#### 示例代码：`TiptapEditor.vue`

```vue
<template>
  <div>
    <div v-if="editor">
      <button @click="editor.chain().focus().toggleBold().run()" 
              :class="{ 'is-active': editor.isActive('bold') }">
        加粗
      </button>
      <button @click="editor.chain().focus().toggleBulletList().run()" 
              :class="{ 'is-active': editor.isActive('bulletList') }">
        列表
      </button>
      </div>

    <editor-content :editor="editor" />
  </div>
</template>

<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'

// 1. 定义编辑器实例
const editor = useEditor({
  // 2. 加载扩展（Extensions）
  extensions: [
    StarterKit,
    // 如果需要图片或链接，需要单独添加相应的扩展
    // Link.configure({ openOnClick: false }),
    // Image, 
  ],
  // 3. 初始内容
  content: '<p>欢迎使用 Tiptap Vue Editor!</p>',
  
  // 4. 监听更新事件（用于数据同步/保存）
  onUpdate: ({ editor }) => {
    // 每次内容变化时，获取 HTML 或 JSON 并处理
    const json = editor.getJSON()
    console.log('内容已更新 (JSON):', json)
    // 可以在这里触发你的 Vuex/Pinia action 来保存数据
  },
})

// 5. 在组件卸载时销毁编辑器实例，防止内存泄漏
// Vue 3 的 onUnmounted 会自动处理 useEditor 实例的销毁
// 但如果你手动创建了 Editor 实例，应该手动调用 editor.destroy()
</script>

<style scoped>
/* 必须为编辑区域添加一些基础样式，Tiptap 不自带样式 */
/* .ProseMirror 是 Tiptap 渲染的根元素类名 */
:deep(.ProseMirror) {
  min-height: 200px;
  padding: 1rem;
  border: 1px solid #ccc;
  outline: none; /* 移除默认焦点轮廓 */
}

.is-active {
  background-color: #eee;
}
</style>
```

### 4\. 关键概念在 Vue 中的体现

| Tiptap 概念 | Vue 实现方式 | 说明 |
| :--- | :--- | :--- |
| **Editor 实例** | `const editor = useEditor(...)` | 使用 `useEditor` 钩子创建和管理编辑器生命周期。 |
| **编辑区域** | `<editor-content :editor="editor" />` | 官方提供的 Vue 组件，用于渲染 ProseMirror 实例。 |
| **触发命令** | `editor.chain().focus().toggleBold().run()` | 在 Vue 方法或 `setup` 逻辑中调用 `editor` 实例上的命令。 |
| **状态查询** | `editor.isActive('bold')` | 用于判断当前选区是否激活了某个 Mark 或 Node，以控制工具栏按钮样式。 |
| **Node View** | 自定义 Vue 组件 | 用于渲染复杂的自定义块（如可拖拽的卡片、交互式嵌入内容），需要通过 `addNodeView` 注册。 |

### 5\. 进阶：自定义 Node View (用 Vue 组件渲染自定义块)

这是 Tiptap 强大之处，允许你用 Vue 组件来渲染和管理编辑器的某个块（Node）。

#### 步骤：

1.  **创建自定义 Vue 组件**：例如，一个可交互的 `CardBlock.vue`。
2.  **创建 Tiptap Extension**：定义一个继承自 `Node` 的自定义扩展。
3.  **注册 Node View**：在扩展的 `addNodeViews` 方法中，使用 `VueNodeViewRenderer` 将 Vue 组件和 Tiptap Node 关联起来。

**核心代码片段（在 Extension 中注册）：**

```javascript
// MyCustomCardExtension.js
import { Node } from '@tiptap/core'
import { VueNodeViewRenderer } from '@tiptap/vue-3'
import CardBlock from './CardBlock.vue'

export default Node.create({
  // ... 其他配置
  addNodeViews() {
    return {
      // 这里的 'cardBlock' 是你在 schema 中定义的 Node 名称
      cardBlock: VueNodeViewRenderer(CardBlock), 
    }
  },
})
```

### 总结

Tiptap 为 Vue 提供了 **`useEditor` 钩子**和 **`EditorContent` 组件**，使得在 Vue 中创建富文本编辑器变得非常直观。由于其 **Headless** 特性，你需要手动编写工具栏的 UI 逻辑，但这也带来了最大的定制自由度，非常适合需要高度定制化界面或复杂交互的 Vue 应用。

您想进一步了解如何实现工具栏按钮的激活状态，还是如何创建自定义的 Node View？
