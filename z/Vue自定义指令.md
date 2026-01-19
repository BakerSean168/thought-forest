---
tags:
  - tech/dev/frontend
  - type/concept
  - status/seed
description: Vue自定义指令
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[ECMAScript MOC]]

---

# Vue 自定义指令 `v-hasPermi` 详解

## 一、概述

`v-hasPermi` 是一个 Vue 自定义指令（Custom Directive），用于实现基于权限的 UI 元素显示控制。它允许开发者通过声明式的方式，根据用户的权限标识（Permission）来控制页面元素的可见性。

## 二、基本语法

```vue
<el-button v-hasPermi="['system:company:create']">
  添加
</el-button>
```

### 语法说明：

- **指令名称**: `v-hasPermi`
- **参数类型**: 字符串数组 `string[]`
- **权限标识**: 采用冒号分隔的三段式格式 `模块:功能:操作`

## 三、权限标识格式解析

```
system:company:create
  ↓      ↓       ↓
模块名  功能名  操作类型
```

### 常见的权限标识示例：

```javascript
// 系统管理模块
'system:company:create'   // 创建公司
'system:company:update'   // 更新公司
'system:company:delete'   // 删除公司
'system:company:query'    // 查询公司
'system:company:export'   // 导出公司数据
'system:company:assign'   // 分配公司管理员

// 学院管理模块
'academy:class:create'    // 创建课程
'academy:class:update'    // 更新课程
'academy:class:delete'    // 删除课程

// 统计模块
'statistics:report:view'  // 查看报表
'statistics:report:export' // 导出报表
```

## 四、指令实现原理

### 4.1 指令定义位置

通常在 `src/directives/permission/index.ts` 中定义：

```typescript
import { useUserStoreWithOut } from '@/store/modules/user'
import type { Directive, DirectiveBinding } from 'vue'

/**
 * 操作权限处理
 */
export const hasPermi: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    const userStore = useUserStoreWithOut()
    const permissions = userStore.getPermissions
    
    if (value && value instanceof Array && value.length > 0) {
      const permissionFlag = value
      const hasPermissions = permissions.some((permission: string) => {
        return permissionFlag.includes(permission)
      })
      
      if (!hasPermissions) {
        // 没有权限，移除 DOM 元素
        el.parentNode && el.parentNode.removeChild(el)
      }
    } else {
      throw new Error('权限标识不能为空')
    }
  }
}
```

### 4.2 指令注册

在 `src/directives/index.ts` 中全局注册：

```typescript
import type { App } from 'vue'
import { hasPermi } from './permission'

export const setupDirectives = (app: App<Element>) => {
  // 注册权限指令
  app.directive('hasPermi', hasPermi)
}
```

### 4.3 在 main.ts 中使用

```typescript
import { createApp } from 'vue'
import App from './App.vue'
import { setupDirectives } from './directives'

const app = createApp(App)

// 注册全局指令
setupDirectives(app)

app.mount('#app')
```

## 五、工作流程

```
1. 用户登录
   ↓
2. 后端返回用户权限列表
   ['system:company:create', 'system:company:update', ...]
   ↓
3. 权限存储到 Pinia Store (userStore)
   ↓
4. 页面渲染时执行 v-hasPermi 指令
   ↓
5. 指令对比用户权限和元素所需权限
   ↓
6. 有权限：显示元素
   无权限：移除 DOM 节点
```

## 六、使用场景

### 6.1 按钮权限控制

```vue
<!-- 添加按钮 -->
<el-button 
  v-hasPermi="['system:company:create']"
  type="primary"
  @click="handleAdd"
>
  添加
</el-button>

<!-- 编辑按钮 -->
<el-button 
  v-hasPermi="['system:company:update']"
  link
  type="primary"
  @click="handleUpdate(row)"
>
  编辑
</el-button>

<!-- 删除按钮 -->
<el-button 
  v-hasPermi="['system:company:delete']"
  link
  type="danger"
  @click="handleDelete(row)"
>
  删除
</el-button>
```

### 6.2 多权限控制（OR 逻辑）

```vue
<!-- 用户拥有任意一个权限即可显示 -->
<el-button 
  v-hasPermi="['system:company:update', 'system:company:delete']"
>
  操作
</el-button>
```

### 6.3 表格列权限控制

```vue
<el-table-column 
  v-hasPermi="['system:company:update', 'system:company:delete']"
  label="操作"
  fixed="right"
>
  <template #default="scope">
    <el-button @click="handleEdit(scope.row)">编辑</el-button>
    <el-button @click="handleDelete(scope.row)">删除</el-button>
  </template>
</el-table-column>
```

### 6.4 区域权限控制

```vue
<div v-hasPermi="['system:company:view']">
  <h3>敏感数据区域</h3>
  <p>只有拥有查看权限的用户才能看到此内容</p>
</div>
```

## 七、与 v-if 的区别

### v-hasPermi（推荐）
```vue
<el-button v-hasPermi="['system:company:create']">
  添加
</el-button>
```
- **优点**: 声明式，代码简洁，语义清晰
- **DOM 处理**: 直接移除 DOM 节点
- **性能**: 不会在 DOM 中留下注释节点

### v-if 方式
```vue
<el-button v-if="checkPermission(['system:company:create'])">
  添加
</el-button>
```
- **优点**: 更灵活，可以添加复杂逻辑
- **DOM 处理**: 使用注释节点占位
- **性能**: 会留下 `<!--v-if-->` 注释

## 八、权限数据流

### 8.1 登录流程

```typescript
// src/store/modules/user.ts
export const useUserStore = defineStore('user', {
  state: () => ({
    permissions: [] as string[]
  }),
  
  actions: {
    async login(loginForm: LoginForm) {
      // 1. 调用登录接口
      const res = await loginApi(loginForm)
      
      // 2. 保存 token
      this.setToken(res.token)
      
      // 3. 获取用户信息和权限
      await this.getInfo()
    },
    
    async getInfo() {
      // 获取用户信息
      const userInfo = await getUserInfo()
      
      // 保存权限列表
      this.permissions = userInfo.permissions || []
      // ['system:company:create', 'system:company:update', ...]
    }
  },
  
  getters: {
    getPermissions(): string[] {
      return this.permissions
    }
  }
})
```

### 8.2 后端权限数据结构

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "user": {
      "id": 1,
      "username": "admin",
      "nickname": "管理员"
    },
    "permissions": [
      "system:company:create",
      "system:company:update",
      "system:company:delete",
      "system:company:query",
      "academy:class:create",
      "academy:class:update"
    ]
  }
}
```

## 九、高级用法

### 9.1 编程式权限检查

在某些场景下，可能需要在 JavaScript 代码中检查权限：

```typescript
import { useUserStore } from '@/store/modules/user'

const userStore = useUserStore()

// 检查单个权限
function checkPermission(permission: string): boolean {
  return userStore.permissions.includes(permission)
}

// 检查多个权限（OR 逻辑）
function checkPermissions(permissions: string[]): boolean {
  return permissions.some(p => userStore.permissions.includes(p))
}

// 检查所有权限（AND 逻辑）
function checkAllPermissions(permissions: string[]): boolean {
  return permissions.every(p => userStore.permissions.includes(p))
}

// 使用示例
if (checkPermission('system:company:create')) {
  // 执行创建操作
}
```

### 9.2 自定义权限指令（AND 逻辑）

如果需要用户同时拥有多个权限才能显示元素：

```typescript
// src/directives/permission/hasAllPermi.ts
export const hasAllPermi: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    const userStore = useUserStoreWithOut()
    const permissions = userStore.getPermissions
    
    if (value && value instanceof Array && value.length > 0) {
      const hasAllPermissions = value.every((permission: string) => {
        return permissions.includes(permission)
      })
      
      if (!hasAllPermissions) {
        el.parentNode && el.parentNode.removeChild(el)
      }
    }
  }
}
```

使用：

```vue
<el-button v-hasAllPermi="['system:company:create', 'system:company:update']">
  同时需要创建和更新权限
</el-button>
```

### 9.3 角色权限指令

```typescript
// src/directives/permission/hasRole.ts
export const hasRole: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    const userStore = useUserStoreWithOut()
    const roles = userStore.getRoles
    
    if (value && value instanceof Array && value.length > 0) {
      const hasRole = roles.some((role: string) => {
        return value.includes(role)
      })
      
      if (!hasRole) {
        el.parentNode && el.parentNode.removeChild(el)
      }
    }
  }
}
```

使用：

```vue
<el-button v-hasRole="['admin', 'manager']">
  管理员或经理可见
</el-button>
```

## 十、最佳实践

### 10.1 权限命名规范

```typescript
// ✅ 推荐：模块:功能:操作
'system:company:create'
'academy:class:update'
'statistics:report:export'

// ❌ 不推荐：过于简单
'create'
'update'

// ❌ 不推荐：过于复杂
'system:company:management:create:new:company'
```

### 10.2 权限粒度控制

```vue
<!-- ✅ 细粒度控制：每个按钮独立权限 -->
<el-button v-hasPermi="['system:company:create']">添加</el-button>
<el-button v-hasPermi="['system:company:update']">编辑</el-button>
<el-button v-hasPermi="['system:company:delete']">删除</el-button>

<!-- ❌ 粗粒度控制：所有操作共用一个权限 -->
<el-button v-hasPermi="['system:company:manage']">添加</el-button>
<el-button v-hasPermi="['system:company:manage']">编辑</el-button>
<el-button v-hasPermi="['system:company:manage']">删除</el-button>
```

### 10.3 避免过度使用

```vue
<!-- ✅ 推荐：在容器级别控制 -->
<el-table-column v-hasPermi="['system:company:update', 'system:company:delete']">
  <template #default="scope">
    <el-button v-hasPermi="['system:company:update']">编辑</el-button>
    <el-button v-hasPermi="['system:company:delete']">删除</el-button>
  </template>
</el-table-column>

<!-- ⚠️ 冗余：整列已经有权限控制 -->
<el-table-column>
  <template #default="scope">
    <el-button v-hasPermi="['system:company:update']">编辑</el-button>
    <el-button v-hasPermi="['system:company:delete']">删除</el-button>
  </template>
</el-table-column>
```

## 十一、安全注意事项

### 11.1 前端权限控制的局限性

```
⚠️ 重要提醒：
前端权限控制仅用于 UI 展示，不能替代后端权限验证！
恶意用户可以通过浏览器开发者工具绕过前端权限检查。
```

### 11.2 双重验证

```typescript
// ✅ 前端：控制 UI 显示
<el-button v-hasPermi="['system:company:delete']" @click="handleDelete">
  删除
</el-button>

// ✅ 后端：验证实际操作权限
@DeleteMapping("/company/{id}")
@PreAuthorize("hasAuthority('system:company:delete')")
public Result deleteCompany(@PathVariable Long id) {
    // 执行删除操作
}
```

## 十二、调试技巧

### 12.1 查看当前用户权限

```typescript
// 在浏览器控制台执行
import { useUserStore } from '@/store/modules/user'
const userStore = useUserStore()
console.log('当前用户权限：', userStore.permissions)
```

### 12.2 临时添加权限（开发环境）

```typescript
// 在浏览器控制台执行
const userStore = useUserStore()
userStore.permissions.push('system:company:create')
```

### 12.3 权限检查工具函数

```typescript
// src/utils/permission.ts
export function debugPermission(permission: string) {
  const userStore = useUserStore()
  const hasPermission = userStore.permissions.includes(permission)
  console.log(`权限检查: ${permission}`)
  console.log(`是否拥有: ${hasPermission}`)
  console.log(`用户权限列表:`, userStore.permissions)
  return hasPermission
}
```

## 十三、总结

`v-hasPermi` 指令是 Vue 权限管理的核心工具，它通过声明式的方式简化了权限控制逻辑，使代码更加清晰易维护。但务必记住：

1. **前端权限仅控制 UI 显示**，真正的安全验证必须在后端实现
2. **权限标识命名要规范**，采用 `模块:功能:操作` 的三段式格式
3. **合理控制权限粒度**，既不能太粗也不能过细
4. **配合路由守卫使用**，实现页面级和元素级的双重权限控制

正确使用 `v-hasPermi` 可以大大提升应用的安全性和用户体验！

