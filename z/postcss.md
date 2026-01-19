---
tags:
  - tech/lang/css
  - type/concept
  - status/growing
description: postcss
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[CSS MOC]] | [[前端基础 MOC]]

---


# PostCSS 详解

## 什么是 PostCSS？

PostCSS 是一个用 JavaScript 工具和插件转换 CSS 代码的工具。  
它本身不是预处理器（如 Sass/Less），而是一个**CSS 处理平台**，通过插件实现各种功能（如自动补全、语法检查、代码转换等）。

## 核心特点

- **插件化**：功能完全由插件实现，按需组合（官方收录插件超 200 个）
- **高性能**：基于 JavaScript 引擎，处理速度快于传统预处理器
- **兼容性**：可将现代 CSS 语法转换为浏览器兼容的代码
- **灵活性**：可与现有工作流（Webpack、Vite 等）无缝集成

## 常用插件

1. **autoprefixer**  
   自动添加浏览器前缀（如 `-webkit-` `-moz-`），根据 Can I Use 数据适配目标浏览器。
2. **postcss-preset-env**  
   允许使用最新 CSS 语法（如 CSS 变量、嵌套、`@custom-media`），自动转换为兼容代码。
3. **cssnano**  
   压缩 CSS 代码（删除空格、合并规则、简写属性等），减小文件体积。
4. **stylelint**  
   CSS 代码检查工具，检测语法错误、规范代码风格。
5. **postcss-import**  
   支持 `@import` 导入 CSS 文件并合并，类似 Sass 的导入功能。

## 工作流程

1. 安装 PostCSS 及所需插件  

   ```bash
   npm install postcss autoprefixer postcss-preset-env --save-dev
   ```

2. 创建配置文件（`postcss.config.js`）  

   ```js
   module.exports = {
     plugins: [
       require('autoprefixer'), // 自动补全前缀
       require('postcss-preset-env')({ stage: 3 }) // 支持现代 CSS 语法
     ]
   }
   ```

3. 集成到构建工具（如 Webpack/Vite）  
   或直接通过 CLI 处理：  

   ```bash
   npx postcss src/style.css -o dist/style.css
   ```

## 与预处理器的区别

- **Sass/Less**：自带语法规则（如变量 `$var`、嵌套），功能固定。  
- **PostCSS**：本身无语法扩展，通过插件实现任意功能，更灵活（可模拟 Sass 语法，也可实现独有的转换）。

## 适用场景

- 需兼容多浏览器的项目（自动补前缀）
- 使用现代 CSS 语法开发（无需等待浏览器完全支持）
- 优化 CSS 体积（压缩、去重）
- 规范团队 CSS 代码风格（配合 stylelint）
