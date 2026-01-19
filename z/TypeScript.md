---
tags:
  - tech/lang/typescript
  - status/growing
description: TypeScript 语言的 MOC
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
moc/parent: TypeScript MOC
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# TypeScript

## 基础概念

核心：在编译阶段进行类型检查，帮助发现潜在错误与增强 IDE 智能提示，不改变运行时行为（默认不插入类型元数据）。

类型系统特性：

1. 结构类型系统（Structural Typing）/ 鸭子类型
2. 类型推断（局部 / 上下文 / 返回值）
3. 宽泛类型 any / unknown / never / void 的语义
4. 联合（|）与交叉（&）
5. 可辨识联合（Discriminated Union）
6. 泛型（Generics）与约束（extends）
7. 条件类型 + 分发特性（Distributive Conditional Types）
8. 映射类型（Mapped Types）与索引类型查询（keyof, Indexed Access）
9. 工具类型 Utility Types（Partial / Pick / Omit / ReturnType / Awaited 等）
10. 模块解析策略（NodeNext / bundler / classic）

常用编译选项（tsconfig.json）：

- strict: 开启所有严格模式
- target / module: 输出目标与模块系统
- moduleResolution: Node / NodeNext
- baseUrl / paths: 路径映射
- esModuleInterop / allowSyntheticDefaultImports: CommonJS 兼容
- skipLibCheck: 跳过声明文件检查（大型项目可加速构建）
- noImplicitAny / noUnusedLocals / noUncheckedIndexedAccess

## 使用指南

### 安装与初始化

```bash
pnpm add -D typescript @types/node
npx tsc --init
```

### 基础示例

```ts
function add(a: number, b: number): number { return a + b }

type User = { id: string; name: string; role?: 'admin' | 'user' }

function getName(u: User) { return u.name }
```

### 常见类型技巧

```ts
// 可辨识联合
type Shape = 
  | { kind: 'circle'; radius: number }
  | { kind: 'rect'; width: number; height: number }

function area(s: Shape) {
  switch (s.kind) {
    case 'circle': return Math.PI * s.radius ** 2
    case 'rect': return s.width * s.height
    default: const _exhaustive: never = s; return _exhaustive
  }
}

// 条件类型 + infer
type PromiseValue<T> = T extends Promise<infer R> ? R : T
type R1 = PromiseValue<Promise<number>> // number

// 映射类型
type ReadonlyDeep<T> = {
  readonly [K in keyof T]: T[K] extends object ? ReadonlyDeep<T[K]> : T[K]
}
```

### any vs unknown

- any：放弃类型检查（逃生舱）
- unknown：类型安全的 any，需要缩小（narrow）后使用

### 类型收窄（Narrowing）

- typeof / instanceof / in / 判空 / 自定义谓词 `arg is Type`

### 模块与声明

- d.ts 声明：为 JS 库补充类型
- declare module / namespace：旧式模式，现代更推荐模块化
- ambient context：全局声明谨慎使用

### 与第三方库

- 使用 `@types/*` 社区类型包
- 对于快速验证：`// @ts-expect-error` 标注预期错误，而不是 `// @ts-ignore`

### 构建集成

- TS + Babel：Babel 仅转语法，不做类型检查 -> 需要单独 `tsc --noEmit`
- SWC/ESBuild：更快转译，仍需 `tsc` 做类型语义验证
- monorepo：使用项目引用（Project References）加速增量编译

## 实战经验

1. 类型驱动设计：先写接口定义，再写实现（防止过度耦合）
2. 避免滥用 any；如果必须，用 `unknown` + 收窄
3. DTO（接口输入输出）与内部领域模型分离
4. API 响应生成类型：结合 `zod` / `io-ts` / `typebox` 进行运行时校验 + 推断类型
5. 使用 `satisfies` 校验字面量结构但保持具体类型
6. 工具类型组合减少重复：`Pick<Base, 'id' | 'name'> & { extra: string }`
7. 尽量避免过深嵌套泛型（复杂读写成本高）
8. 对外发布库时生成 `.d.ts` + 严格导出边界
9. 利用 `tsc --traceResolution` 调试模块解析问题
10. 大型项目配置 `incremental: true` + `composite: true`

## 经验总结

实践重点：明确类型边界（输入 -> 处理 -> 输出）；隔离不可信数据；利用工具类型表达语义；让错误尽早在编译期暴露。

常见反模式：

- 大面积 any / as 断言滥用
- 滥用枚举（优先字面量联合）
- 复杂条件类型嵌套导致可读性差
- 将运行时验证交给 TypeScript（TS 只在编译阶段）

提升策略：

- 约束 eslint 规则：`@typescript-eslint/no-explicit-any`、`consistent-type-imports`
- 分层：interface（公共） vs type（内部组合）
- 错误建模：`Result<T, E>` 或 `Either` 模式
- 生成 API 类型：OpenAPI -> `openapi-typescript` / tRPC 端到端推断

## 信息参考

- 官方文档: <https://www.typescriptlang.org/docs>
- TS 进阶指南: <https://www.typescriptlang.org/tsconfig>
- Utility Types: <https://www.typescriptlang.org/docs/handbook/utility-types.html>
- 深入类型系统：<https://www.typescriptlang.org/docs/handbook/2/types-from-types.html>
- 类型运行时校验：zod (<https://zod.dev>) / io-ts / typebox
- 编译选项可视化：<https://www.typescriptlang.org/tsconfig>

