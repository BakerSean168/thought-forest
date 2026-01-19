---
tags:
  - tech/lang/javascript
  - type/howto
  - status/growing
description: Monaco-Editor
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# Monaco-Editor 

## 基础概念

## 使用指南

### executeEdits（） 方法

```ts

import { ref } from 'vue'

import MonacoEditor from 'monaco-editor-vue3'

import * as monaco from 'monaco-editor' // 必须引入 monaco 核心

  

const value = ref('Test')

const editor = ref<monaco.editor.IStandaloneCodeEditor>() // 严格类型

  

const editorDidMount = (instance: monaco.editor.IStandaloneCodeEditor) => {

  editor.value = instance

  const editorElement = instance.getDomNode()

  editorElement.addEventListener('paste', (e: ClipboardEvent) => {

    e.preventDefault() // 关键：阻止默认粘贴

    const clipboardData = e.clipboardData

    if (!clipboardData) return

    // 获取当前光标位置

    const position = instance.getPosition()

    if (!position) return

    // 创建正确的 Range 对象

    const range = new monaco.Range(

      position.lineNumber,

      position.column,

      position.lineNumber,

      position.column

    )

    // 执行编辑操作

    instance.executeEdits('paste-operation', [{

      range: range,

      text: '插入的内容',

      forceMoveMarkers: true // 保持后续标记位置

    }])

    // 更新光标位置

    const newPosition = position.delta(0, '插入的内容'.length)

    instance.setPosition(newPosition)

  })

}

```

1. 使用 monaco 核心创建 Range
2. 使用 editor 实例来调用 executeEdits 方法

### executeEdits 参数详解

方法签名  

```ts

executeEdits(

  source: string, //操作来源标识

  edits: IIdentifiedSingleEditOperation[], // 操作集合

  endCursorState?: Selection[] | null // 编辑后光标位置状态

): void

```

### 编辑操作对象详解

```ts

interface IIdentifiedSingleEditOperation {

  range: IRange;                   // 编辑范围

  text: string;                    // 插入文本

  forceMoveMarkers?: boolean;      // 是否移动标记

  identifier?: ITextModelResolvedOptions;

}

  

// 创建范围需要 4 个参数：

// startLineNumber, startColumn, endLineNumber, endColumn

const range = new monaco.Range(

  当前行号,

  当前列号,

  当前行号,

  当前列号

)

  

forceMoveMarkers: true

// 表示插入文本后，后面的标记（如断点）会跟随移动

// 如果设为 false，插入文本后的标记保持原位置

```

## 实战经验

## 经验总结

## 信息参考
