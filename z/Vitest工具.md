---
tags:
  - tech/dev/test
  - type/howto
  - status/growing
description: Vitest 单体测试框架
created: 2025-08-07T22:15:07
updated: 2025-12-07T17:00:00
---

> [!info] **上级索引**
> [[前端工程化 MOC]] | [[ECMAScript MOC]]

---

# Vitest 工具

[Vitest 官方文档](https://cn.vitest.dev/?from=home-page.cn)

## 安装与配置

`pnpm add -D vitest @vitest/ui @vitejs/plugin-vue vue-tsc`

```json
"scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui"
  },
```

```ts
export default defineConfig({
  test: {
    include: ['src/**/*.test.ts', 'electron/**/*.test.ts'],
    environment: 'node', // 默认 node
    environmentMatchGlobs: [
      ['src/**/*.test.ts', 'jsdom'],      // src 下测试用 jsdom
      ['electron/**/*.test.ts', 'node'],  // electron 下测试用 node
    ],
  },
})
```
