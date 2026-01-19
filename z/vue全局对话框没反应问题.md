---
tags:
  - tech/dev/frontend
  - type/howto
  - status/seed
description: vue全局对话框没反应问题
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---


**为什么之前没有反应**：

## 🔍 问题分析

### 问题根源：**Vue 响应式实例隔离**

```typescript
// ❌ 之前的错误代码

// 文件1: TaskTemplateCard.vue
import { useMessage } from '@dailyuse/ui';
const message = useMessage(); // 创建了实例 A
message.delConfirm(...);      // 修改实例 A 的 dialog.visible = true

// 文件2: DuMessageProvider.vue
import { useMessage } from '@dailyuse/ui';
const { dialog } = useMessage(); // 创建了实例 B
// 监听的是实例 B 的 dialog.visible

// 结果：实例 A 和实例 B 是两个独立的响应式对象！
// TaskTemplateCard 修改实例 A → DuMessageProvider 监听实例 B → 没有反应 ❌
```

### 为什么会创建不同的实例？

在 Vue 3 的 Composition API 中，**每次调用 `useMessage()` 都会执行函数体，创建新的 `ref` 对象**：

```typescript
export function useMessage() {
  // ⚠️ 每次调用都会创建新的 ref
  const snackbar = ref<SnackbarState>({ ... }); // 新实例
  const dialog = ref<DialogState>({ ... });     // 新实例
  
  return { snackbar, dialog, ... };
}
```

这就像：

```javascript
// 类比：每次调用都创建新对象
function createPerson() {
  return { name: 'John', age: 30 };
}

const person1 = createPerson();
const person2 = createPerson();

person1.age = 40;
console.log(person2.age); // 还是 30，因为是不同的对象！
```

### ✅ 修复方案：全局单例模式

```typescript
// ✅ 修复后的代码

// 全局变量保存唯一实例
let globalMessageInstance: ReturnType<typeof useMessage> | null = null;

export function getGlobalMessage(): ReturnType<typeof useMessage> {
  if (!globalMessageInstance) {
    globalMessageInstance = useMessage(); // 只创建一次
  }
  return globalMessageInstance; // 每次返回同一个实例
}

// 文件1: TaskTemplateCard.vue
const message = getGlobalMessage(); // 获取全局实例
message.delConfirm(...);            // 修改全局实例

// 文件2: DuMessageProvider.vue
const { dialog } = getGlobalMessage(); // 获取同一个全局实例
// 监听的是同一个 dialog 对象

// 结果：共享同一个响应式对象 → 修改立即生效 ✅
```

## 📊 图解说明

### 之前（错误）：

```
TaskTemplateCard.vue          DuMessageProvider.vue
      ↓                              ↓
useMessage()                   useMessage()
      ↓                              ↓
  [实例 A]                       [实例 B]
  dialog: { visible: false }    dialog: { visible: false }
      ↓                              ↓
  修改 visible = true            监听 visible
      ↓                              ↓
  [实例 A] ✓                     [实例 B] ✗
  dialog: { visible: true }     dialog: { visible: false }
                                      ↓
                               没有变化，不渲染对话框 ❌
```

### 修复后（正确）：

```
TaskTemplateCard.vue          DuMessageProvider.vue
      ↓                              ↓
getGlobalMessage()            getGlobalMessage()
      ↓                              ↓
      └──────── [全局实例] ──────────┘
              ↓
         dialog: { visible: false }
              ↓
         修改 visible = true
              ↓
         [全局实例] ✓
         dialog: { visible: true }
              ↓
    两边都看到变化，立即渲染对话框 ✅
```

## 🎯 为什么现在能工作？

因为我们做了两个关键修改：

1. **DuMessageProvider.vue 使用 `getGlobalMessage()`**

   ```typescript
   const { dialog } = getGlobalMessage(); // 获取全局实例
   ```

2. **TaskTemplateCard.vue 也使用 `getGlobalMessage()`**

   ```typescript
   const message = getGlobalMessage(); // 获取同一个全局实例
   ```

现在它们**共享同一个响应式对象**，所以修改能立即反映到 UI 上！

## 💡 类似的例子

这个问题类似于：

```javascript
// ❌ 错误：每个组件都创建自己的 Store
// ComponentA.vue
const store = createStore(); // Store 实例 1

// ComponentB.vue  
const store = createStore(); // Store 实例 2
// 两个 Store 不共享数据！

// ✅ 正确：使用全局单例
// main.ts
const store = createStore(); // 创建一次
app.use(store);              // 全局注册

// 所有组件共享同一个 Store ✅
```

这就是为什么你的 Vuex/Pinia Store 总是能工作——它们默认就是全局单例！

## 📝 总结

**问题本质**：Vue 响应式系统基于引用，不同的 `ref()` 调用会创建不同的响应式对象。

**解决方案**：使用单例模式，确保所有地方访问的是同一个响应式实例。

**经验教训**：需要跨组件共享状态时，应该使用：
- ✅ Pinia Store（推荐）
- ✅ Global Singleton（如 `getGlobalMessage`）
- ✅ Provide/Inject
- ❌ 不要在每个组件中独立调用 `useXxx()`

现在你的删除确认框应该可以正常工作了！🎉
