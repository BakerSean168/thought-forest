---
tags:
  - tech/lang/typescript
  - type/concept
  - status/growing
description: tsc TypeScript编译器指南
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
moc/parent: "TypeScript MOC"
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# tsc 

## 基础概念

TypeScript Compiler，TypeScript 编译器，用来编译 ts 成 js，tsconfig.json 是配置文件，用来配置编译选项。  

### 特点：  
- 专注于 ts 编译，js 编译、类型输出 dts
- 强类型检查
- 单文件编译

### 使用场景：  
- 纯 ts 项目，不需要其他语言，比如 nodejs 项目

### 缺点：  
- 仅仅支持 ts 编译
- 无法代码拆分、模块打包
- 性能差
- 配置繁琐，有时需要借助 babel、postcss、tailwind 等工具

以下是对TypeScript Compiler（tsc）使用指南、实战经验、经验总结和信息参考的补充内容，采用清晰的结构化格式：

---

## 🛠️ 使用指南
1. **安装与基础使用**
```bash
# 全局安装
npm install -g typescript

# 单文件编译
tsc hello.ts
```

2. **核心配置文件（tsconfig.json）**
```json
{
"compilerOptions": {
"target": "ES2020",// 编译目标JS版本
"module": "CommonJS",// 模块系统
"strict": true,// 启用所有严格检查
"outDir": "./dist",// 输出目录
"esModuleInterop": true// 兼容CommonJS/ES模块
},
"include": ["src/**/*.ts"]// 需编译的文件范围
}
```

3. **常用编译命令**
- `tsc`：使用tsconfig.json编译全部文件
- `tsc --watch`：监听文件变动自动编译
- `tsc --noEmit`：只做类型检查不生成JS文件

---

## ⚡ 实战经验
1. **性能优化方案**
- 增量编译：启用 `"incremental": true` 减少重复编译时间
- 项目引用：大型项目使用 `"references"` 拆分子项目
```json
// tsconfig.base.json
{
"references": [{ "path": "./core" }, { "path": "./ui" }]
}
```

2. **与其他工具集成**
- **Babel**：用 `@babel/preset-typescript` 处理新语法，保留tsc类型检查
- **Webpack**：配置 `ts-loader` 或 `babel-loader + fork-ts-checker-plugin`

3. **典型问题解决**
- **类型冲突**：使用 `declare module` 扩展第三方库类型
- **编译慢**：排除测试文件 `"exclude": ["**/*.test.ts"]`
- **路径别名**：配置 `"baseUrl"` 和 `"paths"` 替代相对路径

---

## 📝 经验总结
1. **最佳实践**
- ✅ 始终开启 `strict` 模式避免类型漏洞
- ✅ 分离类型定义（`.d.ts`）与业务代码
- ✅ 使用 `tsc --noEmit` 在CI流程中做类型检查

2. **适用场景评估**
| 场景| 推荐工具| 原因|
|---------------------|---------------|--------------------------|
| 纯TS库开发| ✅ tsc| 类型输出完整|
| 复杂前端项目| ❌ tsc| 需配合打包工具|
| 渐进式TS迁移| ✅ tsc| 支持混合JS/TS编译|

3. **局限性应对**
- **打包能力缺失**：需配合Rollup/Webpack
- **新语法支持延迟**：用Babel补充（如装饰器最新标准）
- **配置复杂度**：使用 `extends` 继承基础配置
```json
{
"extends": "@mycompany/tsconfig/base.json"
}
```

---

## 🔍 信息参考
1. **官方资源**
- [TSConfig参考手册](https://www.typescriptlang.org/tsconfig)
- [编译器选项详解](https://aka.ms/tsconfig-options)

2. **工具链整合**
- [Babel TypeScript预设](https://babeljs.io/docs/babel-preset-typescript)
- [Webpack集成指南](https://webpack.js.org/guides/typescript/)

3. **性能对比**
![编译工具性能对比](https://example.com/tsc-perf-chart) *(注：此处为示意URL)*

---

如需深入某个具体场景（如**如何优化大型项目的编译速度**或**与Vite的集成方案**），我可以提供更针对性的建议。需要我展开哪部分内容呢？ 😊
