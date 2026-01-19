---
tags:
  - tech/dev/architecture
  - type/concept
  - status/seed
description: olp权限架构
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[KRJX MOC]] | [[项目实践 MOC]]

---


让我分析一下这个项目中用户权限管理的完整流程。 

Searched for files matching `**/permission/**`, 4 matches

Read [](file:///d%3A/workProgram/olp-admin/src/directives/permission/hasPermi.ts)

Read [](file:///d%3A/workProgram/olp-admin/src/api/system/permission/index.ts)

Searched for files matching `**/role/**`, 5 matches

Read [](file:///d%3A/workProgram/olp-admin/src/api/system/role/index.ts)

Searched for files matching `**/menu/**`, 4 matches

Read [](file:///d%3A/workProgram/olp-admin/src/api/system/menu/index.ts)

Searched for text `permissions` (`**/src/store/modules/user.ts`), 7 results

Read [](file:///d%3A/workProgram/olp-admin/src/store/modules/user.ts)

Summarized conversation history

Searched for files matching `**/role/RoleAssignMenuForm.vue`, 1 match

Searched for files matching `**/role/RoleForm.vue`, 1 match

Searched for files matching `**/role/index.vue`, 1 match

Searched for files matching `**/menu/MenuForm.vue`, 1 match

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/role/RoleAssignMenuForm.vue#1-1), lines 1 to 150

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/role/RoleForm.vue)

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/menu/MenuForm.vue#1-1), lines 1 to 200

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/menu/MenuForm.vue#180-180), lines 180 to 300

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/role/index.vue#1-1), lines 1 to 200

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/role/index.vue#200-200), lines 200 to 318

Searched for files matching `**/role/RoleDataPermissionForm.vue`, 1 match

Read [](file:///d%3A/workProgram/olp-admin/src/views/system/role/RoleDataPermissionForm.vue#1-1), lines 1 to 200

Searched for regex `v-hasPermi|checkPermission` (`**/src/views/system/user/index.vue`), 8 results

现在为您详细分析 OLP 系统的权限管理机制（CRUD 操作和整体架构）:

## 一、权限系统架构

系统采用 **RBAC (Role-Based Access Control) + 数据权限** 的设计模式:

```
User (用户) 
  ↓ N:M 关系
Role (角色)
  ↓ N:M 关系  
Menu (菜单)
  ↓ 包含
Permission (权限标识)
```

### 核心组成部分:

1. **用户 (User)**: 拥有多个角色
2. **角色 (Role)**: 拥有多个菜单权限 + 数据权限
3. **菜单 (Menu)**: 包含权限标识 (permission 字段)
4. **权限标识**: 格式为 `模块:功能:操作` (如 `system:user:create`)

---

## 二、权限 CRUD 流程

### 1. **创建权限 (Create)**

权限通过菜单管理创建:

```typescript
// 文件: src/views/system/menu/MenuForm.vue
// 菜单类型:
// - 1: 目录 (Catalog) - 不含权限标识
// - 2: 菜单 (Menu) - 可含权限标识  
// - 3: 按钮 (Button) - 必含权限标识

// 表单字段:
formData = {
  name: 'user',           // 菜单编码
  nameCn: '用户管理',      // 中文名
  nameEn: 'User Manage',  // 英文名
  permission: 'system:user:create', // ★ 权限标识
  type: 3,                // 按钮类型
  parentId: 100,          // 父菜单ID
  status: 0,              // 启用状态
  client: 1               // 1-管理端 2-用户端
}

// 提交到后端
await MenuApi.createMenu(data)
```

**权限标识命名规范**:
- `system:user:create` - 创建用户
- `system:user:update` - 修改用户
- `system:user:delete` - 删除用户
- `system:user:query` - 查询用户
- `system:role:assign-menu` - 分配角色菜单权限

---

### 2. **分配权限给角色 (Assign)**

通过"菜单权限"弹窗为角色分配菜单(及其权限):

```typescript
// 文件: src/views/system/role/RoleAssignMenuForm.vue

// 打开分配弹窗
const open = async (row: RoleVO) => {
  // 1. 加载所有菜单树
  menuOptions.value = await MenuApi.getSimpleMenusList()
  
  // 2. 获取角色已有的菜单权限
  formData.menuIds = await PermissionApi.getRoleMenuList(row.id)
  
  // 3. 在树形控件中设置选中状态
  formData.menuIds.forEach((menuId) => {
    treeRef.value.setChecked(menuId, true, false)
  })
}

// 提交分配
const submitForm = async () => {
  const data = {
    roleId: formData.id,
    menuIds: [
      ...treeRef.value.getCheckedKeys(false),     // 全选中节点
      ...treeRef.value.getHalfCheckedKeys()       // 半选中父节点
    ]
  }
  await PermissionApi.assignRoleMenu(data)  // ★ 调用分配接口
}
```

**API 定义**:

```typescript
// src/api/system/permission/index.ts
export interface PermissionAssignRoleMenuReqVO {
  roleId: number
  menuIds: number[]
}

export const assignRoleMenu = (data: PermissionAssignRoleMenuReqVO) => {
  return request.post({ url: '/system/permission/assign-role-menu', data })
}
```

---

### 3. **分配角色给用户 (User-Role Assignment)**

```typescript
// 文件: src/views/system/role/index.vue

// 打开员工选择器
const openUser = async (roleId: number) => {
  formData.value.roleId = roleId
  // 获取角色已分配的用户
  const userIds = await RoleApi.getRoleUsers(roleId)
  selectEmployeeRef.value.open(userIds)
}

// 确认分配用户
const employeeConfirm = async (userList: UserRespVO[]) => {
  formData.value.userIds = userList.map(user => user.id)
  
  await RoleApi.assignRole({
    roleId: formData.value.roleId,
    userIds: formData.value.userIds
  })
}
```

**API 定义**:

```typescript
// src/api/system/permission/index.ts
export interface PermissionAssignUserRoleReqVO {
  userId: number
  roleIds: number[]
}

export const assignUserRole = (data: PermissionAssignUserRoleReqVO) => {
  return request.post({ url: '/system/permission/assign-user-role', data })
}
```

---

### 4. **读取权限 (Read)**

#### 后端返回权限列表:

```typescript
// 登录时后端返回用户信息
const userInfo = {
  user: { id: 1, nickname: 'admin' },
  roles: ['admin', 'editor'],
  permissions: [           // ★ 权限字符串数组
    'system:user:create',
    'system:user:update',
    'system:user:delete',
    'system:role:create',
    '*:*:*'                // 超级管理员权限
  ],
  menus: [...]             // 菜单树
}
```

#### 前端存储权限:

```typescript
// src/store/modules/user.ts
export const useUserStore = defineStore('admin-user', {
  state: () => ({
    permissions: [],  // ★ 权限数组
    roles: [],
    user: {}
  }),
  
  actions: {
    async setUserInfoAction() {
      const userInfo = await getInfo(ClientEnum.ADMIN)
      
      // 存储权限到 state
      this.permissions = userInfo.permissions
      this.roles = userInfo.roles
      
      // 缓存到 localStorage
      wsCache.set(CACHE_KEY.USER, userInfo)
    }
  }
})
```

---

### 5. **前端权限检查 (Check)**

#### v-hasPermi 指令使用:

```vue
<!-- src/views/system/user/index.vue -->

<!-- 新增按钮 - 需要 system:user:create 权限 -->
<el-button 
  v-hasPermi="['system:user:create']"
  @click="handleCreate"
>
  新增
</el-button>

<!-- 编辑按钮 - 需要 system:user:update 权限 -->
<el-button 
  v-hasPermi="['system:user:update']"
  link 
  type="primary"
  @click="handleUpdate(scope.row)"
>
  编辑
</el-button>

<!-- 删除按钮 - 需要 system:user:delete 权限 -->
<el-button 
  v-hasPermi="['system:user:delete']"
  link 
  type="danger"
  @click="handleDelete(scope.row)"
>
  删除
</el-button>
```

#### v-hasPermi 指令实现:

```typescript
// src/directives/permission/hasPermi.ts
import { useCache } from '@/hooks/web/useCache'
const { wsCache } = useCache()

export const hasPermi = {
  mounted(el: HTMLElement, binding: DirectiveBinding) {
    const { value } = binding
    const all_permission = '*:*:*'
    
    // 从缓存获取用户权限
    const permissions = wsCache.get(CACHE_KEY.USER)?.permissions || []

    if (value && value.length > 0) {
      const permissionFlag = value as string[]
      
      // 检查权限
      const hasPermissions = permissions.some((permission: string) => {
        return all_permission === permission || permissionFlag.includes(permission)
      })

      // 无权限则移除 DOM 元素
      if (!hasPermissions) {
        el.parentNode && el.parentNode.removeChild(el)
      }
    }
  }
}
```

**检查逻辑**:
1. 从 `wsCache` 读取用户权限数组
2. 检查是否有 `*:*:*` 超级权限
3. 检查权限数组中是否包含指令要求的权限
4. 无权限则从 DOM 移除元素

---

## 三、数据权限 (Data Scope)

除了功能权限，系统还支持**行级数据权限**:

```typescript
// src/views/system/role/RoleDataPermissionForm.vue

// 数据权限范围 (DICT_TYPE.SYSTEM_DATA_SCOPE)
enum SystemDataScopeEnum {
  ALL = 1,           // 全部数据权限
  DEPT_CUSTOM = 2,   // 自定义部门数据权限
  DEPT_ONLY = 3,     // 本部门数据权限
  DEPT_AND_CHILD = 4,// 本部门及以下数据权限
  SELF = 5           // 仅本人数据权限
}

// 表单数据
formData = {
  id: 1,
  name: '业务员',
  code: 'salesman',
  dataScope: 2,                    // ★ 数据权限范围
  dataScopeDeptIds: [100, 101],    // ★ 自定义部门ID列表
  sectionDataScope: 'SECT_ONLY',   // ★ 科室数据权限
  sectionDataScopeSectIds: [10, 20]// ★ 自定义科室ID列表
}

// 提交数据权限
await PermissionApi.assignRoleDataScope({
  roleId: formData.id,
  dataScope: formData.dataScope,
  dataScopeDeptIds: treeRef.value.getCheckedKeys()
})
```

**API 定义**:

```typescript
// src/api/system/permission/index.ts
export interface PermissionAssignRoleDataScopeReqVO {
  roleId: number
  dataScope: number
  dataScopeDeptIds?: number[]
}

export const assignRoleDataScope = (data: PermissionAssignRoleDataScopeReqVO) => {
  return request.post({ url: '/system/permission/assign-role-data-scope', data })
}
```

---

## 四、完整权限流程图

```
┌─────────────────────────────────────────────────────────────┐
│                    1. 管理员创建菜单/按钮                        │
│              MenuForm.vue → createMenu()                    │
│              设置 permission: "system:user:create"           │
└────────────────────────┬────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    2. 为角色分配菜单权限                        │
│         RoleAssignMenuForm.vue → assignRoleMenu()           │
│         选择菜单树节点,提交 menuIds 数组                        │
└────────────────────────┬────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                   3. (可选) 设置数据权限                        │
│      RoleDataPermissionForm.vue → assignRoleDataScope()     │
│      设置 dataScope, dataScopeDeptIds                       │
└────────────────────────┬────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    4. 为用户分配角色                           │
│            UserForm.vue → roleIds: [1, 2]                   │
│         或 RoleIndex.vue → assignUserRole()                 │
└────────────────────────┬────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                    5. 用户登录获取权限                         │
│            login() → getInfo() → 返回 permissions           │
│            useUserStore.setUserInfoAction()                 │
│            wsCache.set(CACHE_KEY.USER, userInfo)            │
└────────────────────────┬────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────────┐
│                  6. 前端页面权限控制                           │
│         v-hasPermi="['system:user:create']"                 │
│         hasPermi.ts → 检查 permissions 数组                  │
│         有权限: 显示元素 | 无权限: 移除元素                      │
└─────────────────────────────────────────────────────────────┘
```

---

## 五、关键 API 总结

| API 方法 | 文件路径 | 功能 |
|---------|---------|------|
| `createMenu()` | index.ts | 创建菜单(含权限标识) |
| `assignRoleMenu()` | index.ts | 为角色分配菜单权限 |
| `assignUserRole()` | index.ts | 为用户分配角色 |
| `assignRoleDataScope()` | index.ts | 设置角色数据权限 |
| `getRoleMenuList()` | index.ts | 获取角色的菜单权限 |
| `getUserRoleList()` | index.ts | 获取用户的角色列表 |
| `getInfo()` | index.ts | 登录后获取用户信息(含权限) |

---

## 六、权限检查方式

### 1. 指令方式 (推荐)

```vue
<el-button v-hasPermi="['system:user:create']">新增</el-button>
```

### 2. 编程方式

```typescript
import { useUserStore } from '@/store/modules/user'

const userStore = useUserStore()
const hasPermission = userStore.permissions.includes('system:user:create')
```

### 3. 超级管理员权限

拥有 `*:*:*` 权限的用户可绕过所有权限检查。

---

这就是 OLP 系统完整的权限管理机制，采用 RBAC 模型结合数据权限控制，实现了从权限创建、分配、存储到前端检查的完整闭环。
