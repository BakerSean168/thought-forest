---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Lodash
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# Lodash

本文以 Lodash v4.17.21 为准，覆盖基础概念、常用用法、实战经验与参考信息。

## 基础概念

- 简介：Lodash 是常用的 JavaScript 工具库，提供集合、对象、函数、字符串等实用方法，提升开发效率与可读性。
- 发行形态
  - lodash（CommonJS，适合 Node 或非严格 tree-shaking 环境）
  - lodash-es（ESM，适合现代打包工具进行更好 tree-shaking）
- 不变性与副作用
  - 多数方法返回新值（纯函数风格）
  - 注意会“修改第一个参数”的方法：`assign/assignIn`, `merge/mergeWith`, `defaults/Deep` 等会直接变更目标对象
- 版本与安全
  - 使用 >= 4.17.21（旧版曾有原型污染等漏洞）
- 类型支持（TypeScript）
  - `npm i -D @types/lodash` 或 `npm i lodash-es @types/lodash-es -D`

主要是对原生方法的补充或增强

## 使用指南

### 安装

```bash
# Node/通用
npm i lodash
npm i -D @types/lodash

# ESM/更好 tree-shaking
npm i lodash-es
npm i -D @types/lodash-es
```

### 导入方式

- CommonJS

```js
const _ = require('lodash');
const debounce = require('lodash/debounce'); // 按需导入（推荐）
```

- ESM

```js
import _ from 'lodash'; // 可能较大，不利于 tree-shaking
import debounce from 'lodash/debounce'; // 按需导入（CJS 语法的路径）
import { debounce as d2 } from 'lodash-es'; // ESM + tree-shaking（推荐）
```

- TypeScript 提示
  - 若用 `import debounce from 'lodash/debounce'` 需开启 `esModuleInterop: true`
  - 否则使用 `import debounce = require('lodash/debounce')`

### 常用方法速览

- 集合/数组
  - `map, filter, reduce, find, some, every`
  - `groupBy, keyBy, countBy`
  - `uniq, uniqBy, union, difference, intersection`
  - `sortBy, orderBy, chunk, compact, flattenDeep`
  - `sum, sumBy, maxBy, minBy`
- 对象
  - `get, set, has, unset`, `pick, omit, pickBy, omitBy`
  - `merge, mergeWith, defaults, defaultsDeep`, `cloneDeep`, `isEqual`
- 函数
  - `debounce, throttle, once, memoize, defer`
- 字符串与其他
  - `camelCase, kebabCase, snakeCase, capitalize`, `range, times`

### 典型用法示例

- 搜索输入防抖

```js
import debounce from 'lodash/debounce';

const search = debounce((q) => {
  // 调用后端接口
}, 300, { leading: false, trailing: true });

// input.oninput = e => search(e.target.value);
```

- 滚动节流

```js
import throttle from 'lodash/throttle';

const onScroll = throttle(() => {
  // 计算/上报
}, 200, { leading: true, trailing: true });

// window.addEventListener('scroll', onScroll);
```

- 安全读取/设置深层属性

```js
import get from 'lodash/get';
import set from 'lodash/set';

const city = get(user, 'profile.address.city', 'Unknown');
set(user, 'profile.address.city', 'Shanghai'); // 注意：会修改对象
```

- 分组与聚合

```js
import groupBy from 'lodash/groupBy';
import sumBy from 'lodash/sumBy';

const byDept = groupBy(employees, 'dept');
const total = sumBy(orders, 'amount');
```

- 深拷贝与深比较

```js
import cloneDeep from 'lodash/cloneDeep';
import isEqual from 'lodash/isEqual';

const b = cloneDeep(a);
isEqual(a, b); // true
```

## 实战经验

- 体积优化
  - 尽量使用按需导入（`lodash/xxx`）或使用 `lodash-es` 搭配现代打包器
  - 对简单场景优先用原生：`Array.prototype.map/filter/some/every`, `Object.keys`, `URLSearchParams` 等
- 性能与可读性
  - 少用长链式 `_.chain`，用局部变量分步处理更直观，也利于 DCE（死代码消除）
- `merge` 与数组
  - `merge` 对数组是按索引合并而非拼接；如需拼接，使用 `mergeWith` 自定义：

```js
import mergeWith from 'lodash/mergeWith';

const customizer = (objValue, srcValue) => {
  if (Array.isArray(objValue)) return objValue.concat(srcValue);
};
const result = mergeWith({}, a, b, customizer);
```

- `debounce`/`throttle` 细节
  - 选项：`leading` 决定首次触发，`trailing` 决定最后一次是否触发
  - 控制：`fn.cancel()` 取消，`fn.flush()` 立即执行挂起调用
- `memoize` 实用技巧
  - 给复杂入参提供 `resolver(key)`，并可通过 `fn.cache.clear()` 管理缓存

```js
import memoize from 'lodash/memoize';

const fetchUser = memoize(
  async (id) => api.get(`/user/${id}`),
  (id) => id
);
```

- 深拷贝/深比较注意
  - `cloneDeep` 可处理常见内置类型（对象、数组、Map/Set、Date、TypedArray 等）
  - `isEqual` 函数比较为引用相等；循环引用也能正确处理
- 安全与最佳实践
  - 保持依赖为最新稳定版（>= 4.17.21）
  - 避免将不可信对象直接 `merge` 到可到达 `Object.prototype` 的对象
  - 避免在不可信模板字符串中使用 `_.template`
- TypeScript 小贴士
  - `@types/lodash` 覆盖面广，但部分方法返回 `any`，可通过自定义类型封装关键调用
  - 路径字符串的类型不精确时，可用可选链与空值合并替代：`user?.profile?.name ?? 'N/A'`

## 信息参考

- 官方
  - Docs: https://lodash.com/docs
  - GitHub: https://github.com/lodash/lodash
  - lodash-es: https://github.com/lodash/lodash/tree/4.17.21-es
- 速查与工具
  - Cheatsheet: https://devhints.io/lodash
  - Bundle 体积查询: https://bundlephobia.com/package/lodash
- 替代与延伸阅读
  - 原生方法（MDN）：https://developer.mozilla.org/
  - Remeda（TS 友好集合工具）：https://remedajs.com/
  - Ramda（函数式）：https://ramdajs.com/


