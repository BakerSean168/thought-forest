---
tags: [status/growing, type/concept, tech/dev]
description: 我们旨在详细记录 Nx 中的 `project.json` 配置文件的一些知识。
tool: nx
created: 2025-12-30T15:52:39
updated: 2025-12-30T16:00:28
---

# project_json文件详解 

用 Nx 官方文档中的话来说，Project JSON 是 Package JSON 的增强版。 

## 1. 基础元数据 (Project Metadata)

文件顶部的字段定义了项目的基本身份：

- **`name`**: 项目的唯一标识符。你在运行 `nx test <name>` 时使用的就是这个名称。
- **`projectType`**: 标识这是一个 `application`（可独立部署的应用）还是 `library`（被引用的库）。这会影响 Nx 的某些默认构建行为。
- **`sourceRoot`**: 源代码存放的路径，Nx 会根据这个路径来执行 Lint 或单元测试。
- **`tags`**: 标签系统。这是 Nx 最强大的架构约束工具。你可以给项目贴上 `type:ui` 或 `scope:server` 标签，然后在全局规则中规定“禁止 `type:ui` 引用 `scope:server`”，从而防止代码架构腐化。

---

## 2. 核心：`targets` (任务定义)

这是 `project.json` 中最重要的部分，它定义了项目能干什么（Build, Test, Lint 等）。

每个 Target 通常包含以下三个子项：

### A. Executor (执行器)

执行器是“任务的具体实现者”。

- **内置执行器**: 如 `@nx/vite:build` 或 `@nx/jest:jest`。
- **通用执行器**: 如 `nx:run-commands`，允许你直接运行任何终端命令（比如 `rm -rf dist`）。
- **自定义执行器**: 你甚至可以编写自己的执行器来处理特殊的部署逻辑。

### B. Options (基本参数)

这里存放任务的默认配置。

- 例如，`build` 任务下会指定 `outputPath`（输出目录）、`main`（入口文件）等参数。这些参数会被 Nx Console 解析成你看到的交互式表单。

### C. Configurations (配置变体)

用于定义不同环境下的差异。

- 你可以定义 `production` 配置，在其中开启代码压缩（minify）；定义 `development` 配置，开启 SourceMap。运行命令时通过 `-c` 指定：`nx build my-app -c production`。

---

## 3. 任务编排与性能优化

Nx 之所以快，全靠 `project.json` 中的这些高级配置：

### `dependsOn` (任务依赖)

这是解决 Monorepo 编译顺序的神器。

JSON

```
"build": {
  "dependsOn": ["^build"] 
}
```

- **含义**: `^` 符号表示“上游依赖”。这段配置告诉 Nx：“在编译我之前，请先编译我引用的所有 Library 的 build 任务”。

### `outputs` (缓存管理)

Nx 会缓存你的任务结果。

- 通过定义 `"outputs": ["{workspaceRoot}/dist/apps/my-app"]`，Nx 知道这个文件夹里的东西是该任务产出的。
- 下次你运行同样的任务时，如果代码没变，Nx 会直接从缓存里把这个文件夹“变”出来，瞬间完成构建。

---

## 4. 与 `package.json` 的关系总结

如你之前所见，`project.json` 是 `package.json` 的**逻辑增强版**。

|**特性**|**package.json (scripts)**|**project.json (targets)**|
|---|---|---|
|**执行逻辑**|简单的字符串命令|结构化的执行器 (Executors)|
|**任务依赖**|需手动排序或脚本管理|声明式 `dependsOn`|
|**缓存控制**|不支持|颗粒化 `outputs` 和 `inputs`|
|**可视化支持**|仅显示名称|自动生成复杂的 UI 参数表单|

---

## 5. 小技巧：如何快速编辑？

由于 `project.json` 往往很长，建议使用以下方式：

1. **Nx Console 定位**: 在插件侧边栏右键点击项目，选择 "Go to Configuration"。
2. **JSON Schema 支持**: Nx 提供了完善的 Schema 校验。你在 VS Code 中编写 `project.json` 时，会有智能补全和错误提示，告诉你哪些参数是合法的。

## 个人补充

### 使用

如上文所说，Project JSON 是 Package JSON 的增强版。它不仅在细节上增加了控制方法。 

然后，最主要的是它使用了 NX 的内置任务执行系统，所以推荐统一使用 NX 来执行相关任务。 

### 首先是 Name 字段

Project JSON 中的 name 和 Package JSON 中的 name 可以不同。Project JSON 中的 name 主要用于 nx 来区分和标识每一个包；而 Package JSON 中则是用来打包生成的包名。

因此，在 Package JSON 中有必要添加一定的 @ 前缀，以避免命名冲突。然后，Project JSON 中优先使用简洁的名称，可以不需要前缀。 