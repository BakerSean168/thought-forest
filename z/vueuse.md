---
tags:
  - tech/dev/vue
  - type/howto
  - status/growing
description: VueUse 工具库使用指南
created: 2025-10-31T14:17:02
updated: 2025-12-07T17:00:00
---

# VueUse 

**上级索引**：[[Vue3 MOC]]

## 基础概念

- VueUse 是一组基于 Vue 3 Composition API 的实用 composable 函数集合，由 Anthony Fu 发起并维护。
- 目标：加速开发、提高可复用性、遵循 Vue 哲学（组合式、轻量、可树摇）。

快速安装
````javascript
npm install @vueuse/core@14
# 或
yarn add @vueuse/core@14
````

核心概念（简明）
- composable：一个函数（通常以 use 开头）返回 ref/computed/reactive 等响应式值与操作。
- 按需导入：避免 import *，以利于 tree-shaking。
- 自动清理：大多数 composable 会在组件卸载时自动停止/移除监听。
- SSR 感知：运行于服务端时需判断 window 是否可用（部分 composable 提供 isClient 选项或自行 guard）。

常用 composables 与示例
- useWindowSize：监听窗口尺寸
````javascript
import { useWindowSize } from '@vueuse/core';
export default {
  setup() {
    const { width, height } = useWindowSize();
    return { width, height };
  }
}
````

- useLocalStorage：带类型与默认值的持久化 ref
````javascript
import { useLocalStorage } from '@vueuse/core';
const theme = useLocalStorage('theme', 'light'); // ref('light')
````

- useEventListener：统一事件监听
````javascript
import { useEventListener } from '@vueuse/core';
setup() {
  const onClick = () => console.log('clicked');
  useEventListener(window, 'click', onClick);
}
````

- useFetch：基于 fetch 的 composable（支持 abort 与 reactive）
````javascript
import { useFetch } from '@vueuse/core';
const { data, error, isFetching } = useFetch('/api/data').get().json();
````

- useIntervalFn：定时逻辑
````javascript
import { useIntervalFn } from '@vueuse/core';
const { pause, resume } = useIntervalFn(() => { /* doWork */ }, 1000);
````

- usePreferredDark：首选深色模式检测
````javascript
import { usePreferredDark } from '@vueuse/core';
const prefersDark = usePreferredDark();
````

TypeScript 使用
- composable 返回类型通常已推断；可直接解构使用。若需显式类型：
````typescript
import { Ref } from 'vue';
import { useLocalStorage } from '@vueuse/core';
const count: Ref<number> = useLocalStorage<number>('count', 0);
````

SSR 与客户端 Guard
- 在 SSR 环境中访问 window/dom 前请判断：
````javascript
import { isClient } from '@vueuse/core';
if (isClient) {
  // safe to use window/document
}
````

最佳实践与注意事项
- 按需导入：import { useX } from '@vueuse/core'（避免引入整个库）。
- 在大型组件中把副作用分解成多个小 composable，利于测试与复用。
- 对于外部资源（fetch、websocket）使用 composable 封装并确保在组件卸载时清理。
- 如果你要回移植到旧项目（如 BBB 3.0 HTML5），注意依赖的浏览器 API 与 polyfill，以及可能的 Vue 版本差异（仅用于 Vue 3）。
- 检查 composable 的 options（比如 immediate、deep、flush 等）以符合你的业务逻辑。

调试与性能
- composable 内部尽量避免昂贵的同步计算，使用 computed/lazy 计算。
- 对频繁变更的数据做防抖/节流（useDebounce/useThrottle/useDebouncedRef）。

参考与链接
- 官方文档（建议首选阅读）：https://vueuse.org
- GitHub 仓库：https://github.com/vueuse/vueuse
- API 列表（v14）：参见官方 docs 的 API 页面以确认具体参数与返回值

简短迁移提示（若从更高版本移植到你当前项目）
- 对比你使用的具体 composable 在 v14 的签名与选项，确认没有破坏性变更。
- 测试关键路径（SSR、事件监听、localStorage）以确保行为一致。

如果需要，我可以：
- 为你项目中常用的 5~10 个 composable 写具体用例（含 TypeScript）。
- 在你的 BBB 源码中查找并替换非兼容用法，或把某个 composable 的使用改写为原生实现以兼容旧环境。请选择其中一个后续操作。

## 使用指南

## 信息参考
