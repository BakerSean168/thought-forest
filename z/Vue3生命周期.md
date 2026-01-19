---
tags:
   - tech/dev/frontend
   - type/concept
   - status/growing
description: Vue3 生命周期各阶段与钩子说明
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[Vue3]]

---


# Vue3生命周期 

**上级索引**：[[Vue3 MOC]] | [[Vue3]]

## 基础概念

Vue 生命周期是指 Vue 组件实例从创建到销毁的完整过程。  

### 为什么需要声明周期

声明周期本质上是 Vue 组件使用流程中的特点时间点，这些时间点分割出了几个阶段，这几个阶段中有一些重要的特性，让部分操作时需要在不同阶段去执行。  
所以 Vue 给出了生命周期的 API，我们了解生命周期的原理就是为了在适合的时间执行适合的操作。

### Vue 生命周期的四个主要阶段

Vue 组件的生命周期可以分为四个主要阶段，每个阶段包含两个钩子函数(一前一后)：

1. **创建阶段(Creation)**：组件实例的初始化过程
   - beforeCreate
   - created

2. **挂载阶段(Mounting)**：将组件挂载到 DOM 的过程
   - beforeMount
   - mounted

3. **更新阶段(Updating)**：响应数据变化并重新渲染的过程
   - beforeUpdate
   - updated

4. **销毁阶段(Destruction)**：组件实例销毁的过程
   - beforeUnmount(Vue 3)/beforeDestroy(Vue 2)
   - unmounted(Vue 3)/destroyed(Vue 2)

此外，Vue 还提供了 `errorCaptured` 钩子用于错误捕获，以及 `renderTracked` 和 `renderTriggered`(仅开发模式)用于调试。

### 每个生命周期的特点

#### 1. 创建阶段

**beforeCreate**
- 在实例初始化之后数据观测(data observation)和事件/侦听器配置之前被调用
- 此时组件实例刚被创建，`data``methods``computed`等选项还未初始化
- 组合式 API 的 `setup()` 会在所有选项式 API 钩子之前调用，包括 beforeCreate

**created**
- 在实例创建完成后被立即调用
- 此时已完成以下配置：数据观测(data observation)、属性和方法的运算watch/event 事件回调
- `data` 和 `methods` 已初始化完成，可以访问
- 但挂载阶段还未开始，`$el` 属性尚不可用
- 常用于异步数据获取初始化非响应式变量等操作

#### 2. 挂载阶段

**beforeMount**
- 在挂载开始之前被调用，相关的 `render` 函数首次被调用
- 此时已完成模板编译，但尚未将组件挂载到 DOM 上
- 在服务端渲染(SSR)时不会被调用

**mounted**
- 在组件挂载完成后调用
- 组件被视为已挂载的条件：
  - 所有同步子组件都已被挂载(不包含异步组件或 `<Suspense>` 树内的组件)
  - 其自身的 DOM 树已创建并插入父容器中
- 可以访问到渲染后的 DOM，常用于操作 DOM初始化第三方库等
- 在服务端渲染时不会被调用
- 注意：不保证所有子组件也都一起被挂载，如果需要等待整个视图都渲染完毕，可以在内部使用 `this.$nextTick()`

#### 3. 更新阶段

**beforeUpdate**
- 在数据更改导致的虚拟 DOM 重新渲染和打补丁之前调用
- 适合在更新之前访问现有 DOM，比如手动移除已添加的事件监听器
- 可以在这个钩子中进一步更改状态，而不会触发附加的重渲染过程
- 在服务端渲染时不会被调用

**updated**
- 在数据更改导致的虚拟 DOM 重新渲染和打补丁之后调用
- 组件 DOM 已经更新，可以执行依赖于 DOM 的操作
- 注意：**不要**在这个钩子中更改状态，这可能会导致无限的更新循环！
- 对于需要对特定状态变化做出反应的情况，最好使用计算属性或 watcher 代替
- 父组件的 updated 钩子将在其子组件的 updated 钩子之后调用

#### 4. 销毁阶段

**beforeUnmount(Vue 3)/beforeDestroy(Vue 2)**
- 在卸载/销毁组件实例之前调用
- 此时实例仍然完全可用，可以在这里进行清理操作
- 常用于清除定时器取消事件监听取消网络请求等
- 在服务端渲染时不会被调用

**unmounted(Vue 3)/destroyed(Vue 2)**
- 在组件实例卸载/销毁后调用
- 组件被视为已卸载的条件：
  - 其所有子组件都已经被卸载
  - 所有相关的响应式作用(渲染作用计算属性侦听器)都已停止
- 可以执行最终的清理工作，但不能再访问组件实例

#### 5. 错误捕获

**errorCaptured**
- 当捕获到来自后代组件的错误时被调用
- 接收三个参数：错误对象触发错误的组件实例错误来源信息的字符串
- 可以返回 `false` 阻止错误继续向上传播
- 错误来源可能包括：组件渲染事件处理器生命周期钩子setup()函数等

### 使用场景

#### 1. 数据请求

- **created**：适合发起异步数据请求，此时数据观测已建立，能更快获取到数据并触发视图更新
- **mounted**：如果需要访问 DOM 才能发起的请求(如基于元素尺寸的请求)，则在此处发起

#### 2. DOM 操作

- **mounted**：是执行 DOM 操作的最安全位置，此时 DOM 已完全渲染
- **updated**：如果需要在数据变化后操作更新后的 DOM，但要注意避免无限循环

#### 3. 清理工作

- **beforeUnmount**：清除定时器取消事件监听断开 WebSocket 连接等
- 避免在 `unmounted` 中执行重要清理，因为此时可能已无法访问所有必要数据

#### 4. 性能优化

- 避免在 `updated` 中修改状态，这会导致额外的重新渲染
- 对于计算量大的操作，考虑使用 `watch` 或计算属性替代生命周期钩子

#### 5. 父子组件生命周期顺序

- 加载时：父 beforeCreate → 父 created → 父 beforeMount → 子 beforeCreate → 子 created → 子 beforeMount → 子 mounted → 父 mounted
- 更新时：父 beforeUpdate → 子 beforeUpdate → 子 updated → 父 updated
- 销毁时：父 beforeUnmount → 子 beforeUnmount → 子 unmounted → 父 unmounted


## 使用指南

## 实战经验

## 经验总结

## 信息参考

