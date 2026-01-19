---
tags: [status/growing, type/concept]
description: base_template
created: 2025-12-30T16:40:15
updated: 2025-12-30T16:40:52
---

# Vite-Overview 

## 基础概念

### 特点

- 开发环境基于 esbuild
- 按需加载
- 热更新
- 生产构建，rollup

### 使用场景

- 大型业务系统
- Vue、React 业务项目

### 缺点

- 生态
- 低版本兼容，lagecy
- 微信网页开发，本地开发环境，es 模块整合了 esbuild、rollup 的构建工具。  

核心理念：快启动，按需编译，原生模块。

关键点：

1. 开发服务器基于原生 ESM 按需请求（不需预打包全部依赖）
2. 依赖预构建（esbuild）解决 CJS/ESM 兼容与重复解析性能
3. 生产构建使用 Rollup（成熟插件生态 + 高度 Tree-shaking）
4. HMR 极快：模块图（Module Graph）精确失效（invalidate）
5. 插件体系统一：Vite 插件 = Rollup 插件超集（支持 dev + build 钩子）
6. 多框架模板：Vue / React / Svelte / Lit / Preact / Solid
7. 环境变量：`import.meta.env.*` 支持 `.env.*` 文件加载
8. 动态导入 + 代码分块（Lazy Loading）
9. SSR 支持（createServer + transformRequest）
10. 库模式构建（多格式输出）

适用场景：中小型到中大型前端应用、组件库开发、需要快速原型迭代的项目。

## 使用指南

### 初始化

```bash
pnpm create vite my-app --template react-ts
cd my-app && pnpm i && pnpm dev
```

### 目录结构建议

```text
src/
  main.tsx         # 入口
  app/             # 业务模块
  components/      # 可复用组件
  hooks/           # 自定义 hooks
  styles/          # 全局样式
  lib/             # 工具/封装
  env.d.ts         # 类型声明 (import.meta)
```

`vite.config.ts`

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'node:path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  }
})
```

### 静态资源

- 直接 import：`import imgUrl from '@/assets/logo.png'`
- public 目录：`/favicon.svg` 直接访问，不参与 hash 处理

### HMR 调试（补充）

HMR 底层依赖模块图跟踪 import 关系；当文件变更，只失效依赖链，不刷新整页。

### SSR 简单示例（概念）

```ts
import express from 'express'
import { createServer as createViteServer } from 'vite'

async function bootstrap() {
  const app = express()
  const vite = await createViteServer({ server: { middlewareMode: true } })
  app.use(vite.middlewares)
  app.get('*', async (req, res) => {
    const template = await vite.transformIndexHtml(req.url, '<!--app-html-->')
    const { render } = await vite.ssrLoadModule('/src/entry-server.tsx')
    const appHtml = await render()
    res.end(template.replace('<!--app-html-->', appHtml))
  })
  app.listen(5173)
}
bootstrap()
```

## 实战经验

1. 首屏优化：路由懒加载 + 关键 CSS 内联 + 图片占位符（LQIP）
2. 组件库模式：`build.lib` + `rollupOptions.external` 避免打包 react 等 peer 依赖
3. Monaco / ECharts 等大依赖：使用动态 import 或 CDN 外链
4. API Mock：`configureServer` 钩子或使用 `vite-plugin-mock`
5. 环境差异：用 `define` 注入常量（如 APP_VERSION）
6. 拆包策略：将不常用图表/编辑器放独立 chunk，减少主包体积
7. 分析构建：集成 `rollup-plugin-visualizer` 输出产物结构
8. 运行时降级：检测浏览器特性（ESM / import.meta）失败提示升级
9. 使用 `optimizeDeps.include` 提前锁定动态依赖
10. 处理 CJS 动态 require：通过 `optimizeDeps.esbuildOptions` 指定条件

常见坑：

- 路径别名类型需要在 `tsconfig.json` 同步 `paths`
- import.meta.env 访问未定义变量返回 undefined（需定义在 env 文件）
- 一些库使用 Node 内置模块（fs/path）需 polyfill 或在浏览器禁用
- SSR 模式下需要区分 服务端/客户端 逻辑（如 window / document）

## 经验总结

核心收益：极快冷启动 + 精准热更新 + 简单配置。生产构建仍依赖 Rollup，需关注产物拆分与缓存策略。

优化策略：

- 预构建稳定：锁版本 + pnpm store
- 利用浏览器缓存：文件名 hash + long-term cache
- 分析大 chunk：动态异步加载
- 避免全局样式污染：使用 CSS Modules / Scoped / 原子化 CSS

不适合情况：多入口老式多页应用仍需传统构建（可手工配置多入口）。

## 信息参考

- 官方文档: <https://vitejs.dev>
- 配置参考: <https://vitejs.dev/config>
- 插件列表: <https://vitejs.dev/plugins>
- Rollup 文档: <https://rollupjs.org>
- 性能分析：rollup-plugin-visualizer

