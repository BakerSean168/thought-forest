---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Rollup模块打包工具指南
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# Rollup 模块打包工具

## 基础概念

JavaScript 模块打包器，专注于 ES6 模块标准的代码打包，核心优势在于高效的 Tree-shaking（移除未使用代码）。

### 特点

- **Tree-shaking**：静态分析移除未导出/未引用的代码
- **轻量配置**：相比 Webpack 配置更简洁
- **多格式输出**：支持 ES Module (esm)、CommonJS (cjs)、UMD 等
- **插件生态**：官方/社区插件覆盖常见需求
- **模块保留**：`preserveModules` 选项保持源码结构

### 适用场景

| 场景类型| 典型案例|
|-------------------|--------------------------|
| UI 组件库| Vue/React 组件库|
| 工具函数库| lodash-es, vue-use|
| 按需加载方案| 配合动态导入实现|
| 浏览器库开发| D3.js, Three.js 等|

### 局限性

- **大型应用支持弱**：代码分割不如 Webpack 成熟
- **热更新(HMR)**：需额外插件配置
- **性能瓶颈**：超大型项目构建速度较慢
- **学习曲线**：插件系统需时间掌握

---

## 使用指南

### 1. 基础安装

```bash
# 创建项目
npm init -y

# 安装核心包
npm install rollup @rollup/plugin-node-resolve @rollup/plugin-commonjs --save-dev
```

### 2. 最小化配置 (rollup.config.js)

```javascript
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';

export default {
input: 'src/index.js',// 入口文件
output: {
file: 'dist/bundle.js',// 输出路径
format: 'esm',// 输出格式
sourcemap: true// 生成sourcemap
},
plugins: [
resolve(),// 解析node_modules模块
commonjs()// 转换CommonJS模块
]
};
```

### 3. 常用命令

```bash
# 单次打包
npx rollup -c

# 监听模式（实时编译）
npx rollup -c -w

# 生产环境打包（启用压缩）
npx rollup -c --environment NODE_ENV:production
```

### 4. 关键配置项

| 配置项| 作用| 示例值|
|-----------------------|-------------------------------|------------------------|
| `input`| 入口文件路径| `'src/main.js'`|
| `output.file`| 输出文件路径| `'dist/bundle.js'`|
| `output.format`| 模块格式| `'esm'`, `'cjs'`, `'umd'` |
| `output.sourcemap`| 生成sourcemap| `true`|
| `plugins`| 插件列表| `[resolve(), commonjs()]` |
| `external`| 排除外部依赖| `['react', 'lodash']`|

---

## 实战经验

### 1. TypeScript 项目支持

```bash
npm install @rollup/plugin-typescript typescript --save-dev
```

```javascript
// rollup.config.js
import typescript from '@rollup/plugin-typescript';

export default {
plugins: [
typescript({
tsconfig: './tsconfig.json'// 指定TS配置
})
]
};
```

### 2. CSS 处理方案

```bash
npm install rollup-plugin-postcss --save-dev
```

```javascript
import postcss from 'rollup-plugin-postcss';

export default {
plugins: [
postcss({
extract: true,// 提取CSS到独立文件
minimize: true// 压缩CSS
})
]
};
```

### 3. 多入口打包配置

```javascript
export default {
input: {
main: 'src/main.js',
utils: 'src/utils/index.js'
},
output: {
dir: 'dist',// 输出目录
format: 'esm',
entryFileNames: '[name].js'// 入口文件名格式
}
};
```

### 4. 性能优化技巧

```javascript
// rollup.config.js
export default {
treeshake: {
propertyReadSideEffects: false,
moduleSideEffects: false
},
cache: true,// 启用构建缓存
maxParallelFileOps: 4// 并行文件处理数
};
```

---

## 经验总结

### 最佳实践组合

| 场景| 推荐工具链| 优势|
|---------------------|-----------------------------|--------------------------|
| UI组件库| Rollup + TypeScript| 类型安全 + Tree-shaking|
| 工具库| Rollup + Terser| 极致体积优化|
| 复杂应用| Vite (基于Rollup)| 开发体验 + 生产优化|

### 配置优化技巧

1. **路径别名**：使用 `@rollup/plugin-alias` 简化导入

```javascript
import alias from '@rollup/plugin-alias';
plugins: [
alias({
entries: [{ find: '@', replacement: '/src' }]
})
]
```

2. **环境变量**：通过 `rollup-plugin-replace` 注入

```javascript
import replace from '@rollup/plugin-replace';
plugins: [
replace({
'process.env.NODE_ENV': JSON.stringify('production')
})
]
```

### 常见问题解决

| 问题| 解决方案|
|---------------------------|-----------------------------------|
| `Unresolved dependencies` | 添加 `@rollup/plugin-node-resolve` |
| `this is undefined`| 设置 `output.format: 'esm'`|
| 动态导入报错| 添加 `@rollup/plugin-dynamic-import-vars` |
| CSS 未生效| 检查 `postcss` 的 `extract` 配置|

---

## 信息参考

### 官方资源

- [Rollup 官网](https://rollupjs.org/)
- [官方插件列表](https://github.com/rollup/plugins)

### 推荐插件

| 功能| 插件名称|
|---------------------|---------------------------------------|
| TypeScript 支持| `@rollup/plugin-typescript`|
| Babel 转换| `@rollup/plugin-babel`|
| 环境变量| `@rollup/plugin-replace`|
| 图片处理| `@rollup/plugin-image`|
| JSON 导入| `@rollup/plugin-json`|

### 学习资源

- [Rollup 从入门到精通](https://rollupjs.org/introduction/)
- [Tree-shaking 原理解析](https://exploringjs.com/es6/ch_modules.html#static-module-structure)
- [Vite 与 Rollup 对比](https://vitejs.dev/guide/comparisons.html#rollup)

### 企业案例

- **Vue 3**：使用 Rollup 打包核心库
- **React DnD**：基于 Rollup 构建
- **Svelte**：编译工具链采用 Rollup


