---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: React 用于构建用户界面的声明式组件化库
created: 2025-09-28T16:08:26
updated: 2025-12-07T17:00:00
---

# React

**上级索引**：[[000_Home]]

## 前置知识

- [[JavaScript]]
- [[jsx]]
	_JSX and React 是相互独立的 东西。但它们经常一起使用，但你 可以 单独使用它们中的任意一个，JSX 是一种语法扩展，而 React 则是一个 JavaScript 的库。_

## 基础概念

- [[函数式编程]]

核心思想：UI = f(state)。通过单向数据流与不可变数据（推荐实践）提升可预测性。

关键概念：

1. JSX：语法糖 -> 编译生成 `React.createElement(type, props, children)`
2. Virtual DOM：抽象层（Fiber）用于调度、优先级分配、可中断渲染
3. Fiber：链表化的工作单元，支持时间切片、并发特性
4. Component：函数组件（推荐） vs Class 组件（遗留兼容）
5. Hooks：`useState / useEffect / useMemo / useCallback / useRef / useReducer / useContext / useLayoutEffect / useImperativeHandle / useTransition / useDeferredValue`
6. 状态分层：本地 UI 状态 / 共享（Context / 状态库）/ 服务器同步状态（react-query 等）
7. 渲染阶段 vs 提交阶段：渲染可中断，提交（DOM 操作）不可中断
8. 协调（Reconciliation）：基于 key 的最小化 diff
9. 严格模式（StrictMode）：开发阶段双调用副作用用于检测不安全逻辑
10. 并发特性：`startTransition`、`Suspense`、`useTransition`、`useDeferredValue`

何时用 React：需要高度交互、组件复用、生态依赖（丰富的第三方库），而非仅静态内容展示。

### components




## 使用指南

### 创建项目

推荐使用 Vite：

```bash
pnpm create vite my-app --template react-ts
```

### 函数组件最小例

```tsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

### useEffect 常见模式

```tsx
// 订阅 + 清理
useEffect(() => {
  const id = setInterval(() => setTick(t => t + 1), 1000);
  return () => clearInterval(id);
}, []);

// 依赖变化触发
useEffect(() => {
  fetchData(query);
}, [query]);
```

避免陷阱：

- 不要把所有逻辑塞进一个 effect；按职责拆分
- 依赖数组缺失变量会导致陈旧闭包（stale closure）
- 使用 `useRef` 持久化可变值不触发重渲染

### 性能优化

- 避免不必要状态：能由 props / 计算得出的就不要存 state
- 多用派生计算：`useMemo` 包裹重计算；谨慎使用，优先清晰代码
- 事件回调稳定：`useCallback` 在子组件依赖时使用
- 列表加稳定 `key`，不使用 index（除非静态列表）
- 大列表 -> 虚拟化（react-window / react-virtualized）
- 图片懒加载 / 代码拆分 `React.lazy + Suspense`
- 利用 `startTransition` 降低非紧急更新优先级

### 状态管理选择

- 简单：组件内 state + props 传递
- Context：层级传递避免 prop drilling（轻量共享配置）
- Redux Toolkit：复杂状态 + 可追踪调试
- Zustand / Jotai / Recoil：语义简单、细粒度订阅
- 服务器状态：TanStack Query（缓存 + 重新获取 + 并发）

### Suspense 典型用法

```tsx
<Suspense fallback={<Spinner/>}>
  <UserProfile id={id} />
</Suspense>
```
结合数据请求库（如 React 18 搭配 RSC 或 Relay）更佳。

## 实战经验

1. 组件分层：基础展示（UI）/ 容器（数据协调）/ 业务复合（流程逻辑）
2. 不在渲染时执行副作用：副作用放入 `useEffect`
3. 跨组件通信尽量提升到最近公共祖先；过多 Context 需关注嵌套与性能（可拆分 Provider）
4. 表单：受控 vs 非受控（`defaultValue` + `ref`）；性能敏感表单可局部受控
5. 列表渲染大数据：分页 + 虚拟滚动 + 服务端排序
6. 错误边界：捕获渲染/生命周期错误（函数组件需外层 class ErrorBoundary）
7. SSR/同构：Next.js / Remix；注意只在浏览器存在的 API 用 `useEffect` 延迟
8. Hook 复用：抽象 `useFetch / useToggle / useDebounce / usePrevious`
9. 避免滥用全局状态：UI 短暂态（弹窗开关）保留在局部
10. 并发特性实验：交互时使用 `startTransition` 避免卡顿

组件库：  
[[react-intl]] 国际化

## 经验总结

思维模型：分离纯渲染与副作用；状态向下流动，事件向上传递。

复用策略：
- Hook（逻辑） + 组件（结构） + 样式（样式变量或 Tailwind）解耦
- 优先组合而非继承

调试：React DevTools Components / Profiler；性能瓶颈多来自重复渲染（与数据结构设计相关）。

反模式：

- 在 render 中发请求
- 深层 prop drilling（用 Context 或自定义 hook）
- 过度使用 `memo` / `useCallback` 造成心智负担
- 不可预期副作用依赖（缺少依赖声明）

## 信息参考

- 官方文档: <https://react.dev>
- 旧版文档（迁移对比）: <https://legacy.reactjs.org>
- Hooks FAQ: <https://react.dev/reference/react>
- Dan 关于 Effects 的文章：<https://overreacted.io/>
- React 性能与 Fiber：<https://react.dev/learn/render-and-commit>
- 状态管理对比：Redux Toolkit / Zustand / Jotai / Recoil / MobX
- [React 中文文档](https://zh-hans.react.dev/learn)
