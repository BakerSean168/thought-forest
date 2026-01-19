tags:
  - tech/lang/typescript
  - type/concept
  - status/growing
description: tsup - 零配置 TypeScript 打包工具
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
moc/parent: "TypeScript MOC"

> [!info] **上级索引**
> [[前端工程化 MOC]] | [[ECMAScript MOC]]


# tsup 打包工具

tsup 是基于 esbuild 的零配置 TypeScript 打包工具，专为库开发者设计。

## 基础概念

### 技术架构

- **底层引擎**：基于 Go 语言开发的 esbuild，构建速度极快
- **开箱即用**：默认支持 TypeScript，无需额外配置
- **专注库打包**：为工具库、CLI 工具等场景优化

### 核心特点

| 特点 | 说明 |
|------|------|
| 零配置 | 默认支持 TS/JS，自动处理 .d.ts 生成 |
| 性能卓越 | 基于 esbuild，构建速度比 Webpack 快 10-100 倍 |
| 多格式输出 | 支持 ESM、CJS、IIFE 多种格式 |
| 代码分割 | 支持动态导入和代码分割 |
| Watch 模式 | 内置文件监听，开发体验友好 |

### 适用场景

- ✅ **工具库**：lodash 风格的工具函数库
- ✅ **脚手架 CLI**：命令行工具打包
- ✅ **npm 包**：发布到 npm 的库
- ✅ **Monorepo 内部包**：workspace 内的共享包
- ❌ **大型应用**：复杂业务应用建议用 Vite/Webpack

### 局限性

- 插件生态不如 Rollup/Webpack 丰富
- 不适合复杂的业务应用构建
- CSS 处理能力有限

## 使用指南

### 安装

```bash
npm install tsup -D
# 或
pnpm add tsup -D
```

### 基础用法

```bash
# 打包入口文件
tsup src/index.ts

# 多种格式输出
tsup src/index.ts --format cjs,esm

# 生成类型声明
tsup src/index.ts --dts
```

### 配置文件

创建 `tsup.config.ts`：

```typescript
import { defineConfig } from 'tsup';

export default defineConfig({
  entry: ['src/index.ts'],
  format: ['cjs', 'esm'],
  dts: true,           // 生成 .d.ts
  splitting: false,    // 代码分割
  sourcemap: true,     // Source Map
  clean: true,         // 清理输出目录
  minify: true,        // 压缩代码
});
```

### 多入口配置

```typescript
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    cli: 'src/cli.ts',
    utils: 'src/utils/index.ts',
  },
  format: ['cjs', 'esm'],
  dts: true,
});
```

### package.json 配置

```json
{
  "name": "my-library",
  "main": "./dist/index.js",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "require": "./dist/index.js",
      "import": "./dist/index.mjs",
      "types": "./dist/index.d.ts"
    }
  },
  "scripts": {
    "build": "tsup",
    "dev": "tsup --watch"
  }
}
```

## 实战经验

### 与 Rollup 对比

| 对比项 | tsup | Rollup |
|--------|------|--------|
| 构建速度 | ⚡ 极快 | 🐌 较慢 |
| 配置复杂度 | 简单 | 中等 |
| 插件生态 | 较少 | 丰富 |
| Tree-shaking | 支持 | 更精细 |
| 适用场景 | 小型库 | 大型库 |

### 最佳实践

1. **保持简单**：利用零配置优势，避免过度配置
2. **类型优先**：始终开启 `dts: true` 生成类型声明
3. **双格式发布**：同时输出 CJS 和 ESM 保证兼容性
4. **Watch 开发**：使用 `--watch` 提升开发效率

## 参考资料

- **官方文档**：[github.com/egoist/tsup](https://github.com/egoist/tsup)
- **esbuild**：[esbuild.github.io](https://esbuild.github.io/)
- **相关对比**：[[rollup与tsup对比]]
