---
tags:
  - tech/lang/css
  - type/concept
  - status/growing
description: UnoCSS 原子化 CSS 引擎
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[CSS MOC]] | [[前端工程化 MOC]]

---

# Unocss

## 基础概念

### 什么是 Unocss？
**Unocss** 是一个高性能的原子化 CSS 引擎，由 [Anthony Fu](https://github.com/antfu)（Vite、Vue 核心团队成员）开发。它类似于 **Tailwind CSS**，但更轻量、更灵活，并且深度集成现代前端工具链（如 Vite、Webpack、Nuxt、Next.js）。

#### 核心特点：
1. **按需生成 CSS**：只生成实际使用的样式，无冗余代码。
2. **极速 HMR（热更新）**：比传统 CSS 方案快 10~100 倍。
3. **高度可定制**：支持自定义规则、动态样式、主题系统。
4. **零运行时**：生产环境生成静态 CSS，无 JavaScript 开销。

#### 与 Tailwind CSS 的对比：
| **特性**| **Unocss**| **Tailwind CSS**|
|-------------------|-------------------------------------|---------------------------------|
| **体积**| 更小（核心 < 10KB）| 较大（包含所有工具类）|
| **灵活性**| 规则完全可编程| 配置固定，扩展需修改配置|
| **性能**| 按需生成，HMR 极快| 需扫描文件，HMR 较慢|
| **预设生态**| 兼容 Tailwind/Windi CSS 预设| 依赖官方插件体系|

---

## 使用指南

### 1. 安装与配置
#### 在 Vite 项目中集成：
```bash
npm install -D unocss @unocss/vite
```

#### 配置 `vite.config.ts`：
```ts
import { defineConfig } from 'vite'
import Unocss from 'unocss/vite'

export default defineConfig({
plugins: [Unocss()]
})
```

#### 创建 `uno.config.ts`（可选自定义规则）：
```ts
import { defineConfig, presetUno } from 'unocss'

export default defineConfig({
presets: [presetUno()], // 默认包含 Tailwind/Windi 兼容规则
rules: [
// 自定义规则，如：
['m-1', { margin: '4px' }]
]
})
```

### 2. 基本使用
在代码中直接使用原子化类名：
```html
<div class="m-4 p-2 bg-blue-500 hover:bg-red-500">Hello Unocss!</div>
```

#### 动态样式：
```html
<div class="text-${color}">Dynamic Color</div>
```
需在配置中开启安全列表：
```ts
safelist: ['text-red-500', 'text-blue-500']
```

### 3. 高级功能
#### 自定义主题：
```ts
// uno.config.ts
export default defineConfig({
theme: {
colors: {
primary: '#3366FF'
}
}
})
```
使用：
```html
<button class="bg-primary">Primary Button</button>
```

#### 指令系统（Directives）：
```css
/* 在 CSS 中批量应用 Unocss 规则 */
.btn {
@apply py-2 px-4 rounded;
}
```

---

## 实战经验

### 技巧 1：与 Vue/React 深度集成
#### Vue SFC 中的 `<style>` 支持：
```vue
<style>
/* 自动扫描 <template> 中的类名 */
@unocss-placeholder
</style>
```

#### React + TypeScript 类型提示：
安装类型包：
```bash
npm install -D @unocss/types
```
在 `tsconfig.json` 中：
```json
{
"compilerOptions": {
"types": ["@unocss/types"]
}
}
```

### 技巧 2：优化生产构建
#### 生成独立 CSS 文件：
```ts
// uno.config.ts
export default defineConfig({
output: {
file: 'public/uno.css' // 输出到静态目录
}
})
```

#### 检查未使用的样式：
```bash
npx unocss --lint
```

---

## 经验总结

### 适用场景
1. **追求极致性能**：适合大型项目或对 HMR 速度敏感的开发环境。
2. **高度定制需求**：需要动态生成样式或非标准工具类时。
3. **微前端/Monorepo**：按需生成特性可减少样式冲突。

### 常见问题
- **类名冲突**：通过 `prefix` 配置添加命名空间（如 `u-m-4`）。
- **学习成本**：熟悉原子化 CSS 概念后，迁移成本较低。

---

## 信息参考
- 官方文档：[unocss.dev](https://unocss.dev/)
- 预设列表：[GitHub - unocss/presets](https://github.com/unocss/unocss/tree/main/packages/presets)
- 示例项目：[Vite + Unocss Template](https://github.com/antfu/vitesse)
