---
tags:
  - tech/dev/project
  - type/howto
  - status/growing
description: OLP项目权限管理系统设计与实现
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[项目文档 MOC]] | [[OLP MOC]]

---


# Company 公司管理模块业务分析

## 一、业务背景

### 1.1 模块定位

Company 模块是 OLP（在线学习平台）系统中的**组织架构管理核心模块**，负责管理整个平台的公司组织结构。该模块位于系统管理（System Management）板块下，是平台多租户架构的基础。

### 1.2 业务价值

1. **多租户隔离**: 支持不同公司独立管理各自的培训数据
2. **层级管理**: 构建公司-供应商的树形组织结构
3. **权限基础**: 为用户权限分配提供组织维度的数据隔离
4. **数据统计**: 按公司维度进行培训数据的统计和报表分析

## 二、核心业务概念

### 2.1 公司类型（Company Type）

系统定义了两种公司类型：

```typescript
export enum CompanyTypeEnum {
  SERVICE = 0,    // 服务公司（甲方）
  SUPPLIER = 1    // 供应商（乙方/承包商）
}
```

#### 服务公司（Service Company）
- **定义**: 平台的主要客户，培训服务的需求方
- **特征**: 
  - 不需要上级公司
  - 可以拥有多个供应商
  - 通常是组织树的根节点
- **示例**: 某银行、某企业集团

#### 供应商（Supplier/Contractor）
- **定义**: 为服务公司提供培训服务的第三方机构
- **特征**:
  - 必须隶属于某个服务公司
  - 可以是多级供应商结构
  - 需要分配合同管理员
- **示例**: 培训机构、咨询公司、外包团队

### 2.2 组织层级结构

```
服务公司 A（Service Company）
├── 供应商 A1（Supplier）
│   ├── 子供应商 A1-1
│   └── 子供应商 A1-2
├── 供应商 A2（Supplier）
└── 供应商 A3（Supplier）

服务公司 B（Service Company）
├── 供应商 B1（Supplier）
└── 供应商 B2（Supplier）
```

### 2.3 合同管理员（Contract Holder）

```typescript
interface UserContractHolderRespVO {
  id: number
  companyId: number      // 公司ID
  userId: number         // 用户ID
  createTime: Date
}
```

**业务含义**:
- 每个供应商公司可以指定多个合同管理员
- 合同管理员负责该供应商与服务公司之间的业务对接
- 管理员有权查看和管理该供应商的培训项目

## 三、核心业务功能分析

### 3.1 公司查询与筛选

```typescript
const queryParams = reactive({
  name: undefined,              // 公司名称
  status: undefined,            // 状态（启用/禁用）
  type: undefined,              // 类型（服务公司/供应商）
  serviceCompanyId: undefined,  // 上级服务公司
  deptCode: undefined,          // 唯一编码
  dataSource: undefined,        // 数据来源（手动/MDS/导入）
})
```

#### 搜索场景：

1. **按公司名称搜索**
   - 用途：快速定位特定公司
   - 示例：搜索 "ABC 培训公司"

2. **按类型筛选**
   - 服务公司视图：只显示甲方客户
   - 供应商视图：只显示外包商

3. **按上级公司筛选**
   - 场景：查看某服务公司下的所有供应商
   - 示例：查看 "银行 A" 合作的所有培训机构

4. **按数据来源筛选**
   - MDS：从主数据系统同步的公司
   - IMPORT：批量导入的公司
   - 手动创建：系统内手动添加

### 3.2 新增公司业务流程

#### 流程图：

```
开始
  ↓
选择公司类型（服务公司/供应商）
  ↓
[如果是供应商] → 选择上级服务公司（必填）
  ↓
填写公司信息
  - 公司全称
  - 简称
  - 排序
  - 唯一编码
  - 状态
  ↓
提交保存
  ↓
刷新列表
```

#### 代码实现：

```typescript
const handleAdd = async (row: CompanyRespVO) => {
  reset()
  
  // 加载所有公司用于选择上级公司
  const response = await listCompany()
  deptOptions.value = handlePhaseTree(response, 'id')
  
  // 如果是从某个公司下新增，自动设置上级公司
  if (row !== undefined) {
    form.value.serviceCompanyId = row.id
  }
  
  deptParamsId.value = row.id
  open.value = true
  title.value = t('sys.company.addCompany')
}
```

#### 业务规则：

1. **供应商必须选择上级服务公司**
   ```typescript
   // 验证规则
   if (form.value.type === CompanyTypeEnum.SUPPLIER) {
     // serviceCompanyId 必填
     rules.serviceCompanyId = [
       { required: true, message: '请选择上级服务公司' }
     ]
   }
   ```

2. **服务公司不能选择上级公司**
   ```typescript
   if (form.value.type === CompanyTypeEnum.SERVICE) {
     // 清空上级公司字段
     delete form.value.serviceCompanyId
     delete form.value.parentId
   }
   ```

3. **唯一编码规则**
   - MDS 来源：不可编辑（由主数据系统维护）
   - IMPORT 来源：不可编辑（导入时已确定）
   - 手动创建：可编辑

### 3.3 编辑公司业务流程

```typescript
const handleUpdate = async (item: CompanyRespVO) => {
  deptParamsId.value = item.id
  reset()
  open.value = true
  
  // 如果是供应商，加载可选的上级公司
  if (item.type === CompanyTypeEnum.SUPPLIER) {
    // 排除自身及其子公司（避免循环引用）
    const data = await listCompanyExcludeChild(item.id as number)
    deptOptions.value = handlePhaseTree(data, 'id')
  }
  
  // 加载公司详情
  const data = await getCompany(item.id as number)
  form.value = data
  
  title.value = t('sys.company.editCompany')
}
```

#### 关键业务逻辑：

1. **防止循环引用**
   ```
   示例：
   公司 A
   └── 供应商 B
       └── 子供应商 C
   
   编辑供应商 B 时：
   - ✅ 可以选择公司 A 作为上级
   - ❌ 不能选择自己（B）
   - ❌ 不能选择自己的子公司（C）
   ```

2. **数据来源保护**
   ```typescript
   // MDS 和 IMPORT 来源的公司编码不可修改
   :disabled="form.dataSource === 'MDS' ? true : 
              form.dataSource === 'IMPORT' ? true : false"
   ```

### 3.4 删除公司业务流程

```typescript
const handleDelete = async (item: CompanyRespVO) => {
  try {
    // 1. 删除二次确认
    await message.delConfirm()
    
    // 2. 调用删除接口
    await delCompany(item.id)
    
    // 3. 提示成功
    message.success(t('common.delSuccess'))
    
    // 4. 刷新列表
    await getList()
  } catch {}
}
```

#### 删除限制（后端实现）：

1. **存在下级公司时不可删除**
   - 提示：请先删除或迁移下级公司

2. **存在关联培训数据时不可删除**
   - 存在培训课程
   - 存在学员记录
   - 存在合同管理员

3. **MDS 来源数据的删除**
   - 可能需要同步删除主数据系统
   - 或者只能标记为禁用，不能物理删除

### 3.5 分配合同管理员

```typescript
const handleAssign = async (id: number) => {
  formData.value.companyId = id
  formData.value.userIds = []
  queryContractHolder.companyId = id
  
  // 1. 查询当前已分配的管理员
  const res = await getUserContractHolderPage(queryContractHolder)
  const ids = res.list?.map(
    (userContractHolder: UserContractHolderRespVO) => 
      userContractHolder.userId
  )
  
  // 2. 打开员工选择器，预选已分配的管理员
  selectEmployeeRef.value.open(ids)
}

const employeeConfirm = async (userData: UserRespVO) => {
  // 3. 保存选择的管理员
  formData.value.userIds = userData?.map((user: UserRespVO) => user.id)
  
  await createUserContractHolder(formData.value)
  message.success('Created successfully')
  getList()
}
```

#### 业务场景：

**场景 1：供应商对接管理**
```
服务公司：某银行
供应商：ABC 培训公司
合同管理员：张三、李四

业务流程：
1. 银行与 ABC 签订培训服务合同
2. 为 ABC 公司分配合同管理员张三和李四
3. 张三、李四可以：
   - 创建培训项目
   - 管理培训学员
   - 查看培训统计报表
   - 对接银行培训需求
```

**场景 2：多级供应商管理**
```
服务公司：某集团
一级供应商：培训机构 A
  └── 二级供应商：讲师团队 B

管理员分配：
- 培训机构 A 的管理员：管理 A 的所有培训项目
- 讲师团队 B 的管理员：只管理 B 承接的培训项目
```

## 四、权限控制分析

### 4.1 页面级权限

```vue
<el-button 
  v-hasPermi="['system:company:create']"
  @click="handleAdd"
>
  添加
</el-button>
```

### 4.2 权限矩阵

| 操作 | 权限标识 | 说明 | 适用角色 |
|------|---------|------|---------|
| 查询公司列表 | `system:company:query` | 查看公司信息 | 所有管理员 |
| 新增公司 | `system:company:create` | 创建新公司 | 系统管理员 |
| 编辑公司 | `system:company:update` | 修改公司信息 | 系统管理员 |
| 删除公司 | `system:company:delete` | 删除公司 | 超级管理员 |
| 分配管理员 | `system:company:assign` | 为公司分配合同管理员 | 系统管理员 |

### 4.3 数据权限

```typescript
// 后端实现示例
@GetMapping("/list")
@PreAuthorize("hasAuthority('system:company:query')")
public Result<List<Company>> listCompany() {
    Long userId = SecurityUtils.getUserId()
    User user = userService.getById(userId)
    
    // 1. 超级管理员：查看所有公司
    if (user.isAdmin()) {
        return companyService.listAll()
    }
    
    // 2. 服务公司管理员：只能看到自己公司及其供应商
    if (user.getCompanyType() == CompanyTypeEnum.SERVICE) {
        return companyService.listByServiceCompanyId(user.getCompanyId())
    }
    
    // 3. 供应商管理员：只能看到自己公司
    return companyService.listByCompanyId(user.getCompanyId())
}
```

## 五、数据来源管理

### 5.1 数据来源类型

| 来源 | 说明 | 特点 | 可编辑字段 |
|------|------|------|-----------|
| 手动创建 | 系统内手动添加 | 完全可控 | 全部 |
| MDS | 主数据系统同步 | 定期同步 | 部分（编码不可改） |
| IMPORT | Excel 批量导入 | 一次性导入 | 部分（编码不可改） |

### 5.2 数据同步流程

```
MDS 系统
  ↓ (定时任务)
数据同步服务
  ↓
OLP 系统 Company 表
  ↓
前端展示
```

#### 同步规则：

1. **增量同步**
   - 每天凌晨 2 点执行
   - 只同步新增和更新的公司
   - 不删除 OLP 系统中的公司

2. **字段映射**
   ```typescript
   MDS 字段 → OLP 字段
   org_code → deptCode
   org_name → deptName
   short_name → shortName
   parent_id → serviceCompanyId
   status → status
   ```

3. **冲突处理**
   - MDS 数据优先级最高
   - 如果 OLP 中已手动修改，同步时会覆盖
   - 关键字段（如 deptCode）不允许 OLP 端修改

## 六、业务场景示例

### 场景 1：新客户入驻

```
1. 背景：
   - 新银行客户入驻平台
   - 银行有 3 家合作的培训机构

2. 操作步骤：
   Step 1: 创建服务公司
   - 公司名称：XX 银行
   - 类型：服务公司
   - 状态：启用
   
   Step 2: 创建供应商
   - 上级公司：XX 银行
   - 供应商 1：金融培训学院
   - 供应商 2：管理咨询公司
   - 供应商 3：IT 培训机构
   
   Step 3: 分配合同管理员
   - 为每个供应商分配 2-3 名管理员
   - 管理员负责对接银行培训需求

3. 结果：
   - 组织架构搭建完成
   - 可以开始创建培训项目
```

### 场景 2：供应商更换

```
1. 背景：
   - 某供应商服务质量不佳
   - 需要更换新的供应商

2. 操作步骤：
   Step 1: 禁用旧供应商
   - 状态改为：禁用
   - 保留历史培训数据
   
   Step 2: 添加新供应商
   - 创建新供应商公司
   - 分配合同管理员
   
   Step 3: 迁移培训项目
   - 将未完成的培训项目转移给新供应商
   - 更新学员归属

3. 注意事项：
   - 旧供应商的历史数据不删除（用于统计分析）
   - 旧供应商的管理员权限收回
```

### 场景 3：多级供应商管理

```
1. 背景：
   - 大型培训机构作为一级供应商
   - 该机构有多个讲师团队作为二级供应商

2. 组织结构：
   服务公司：某集团
   └── 一级供应商：领先培训机构
       ├── 二级供应商：金融讲师团队
       ├── 二级供应商：IT 讲师团队
       └── 二级供应商：管理讲师团队

3. 权限划分：
   - 集团管理员：查看所有数据
   - 领先培训机构管理员：管理所有讲师团队
   - 各讲师团队管理员：只管理自己的培训项目
```

## 七、表格展示分析

### 7.1 列配置

```vue
<el-table :data="deptList" row-key="id">
  <!-- 公司名称 -->
  <el-table-column prop="name" label="公司名称" width="260" fixed="left" />
  
  <!-- 简称 -->
  <el-table-column prop="shortName" label="简称" width="260" />
  
  <!-- 类型（是否为供应商） -->
  <el-table-column prop="type" label="类型" width="260">
    <template #default="scope">
      <span>{{ scope.row.type === CompanyTypeEnum.SERVICE 
        ? '否' : '是' }}</span>
    </template>
  </el-table-column>
  
  <!-- 唯一编码 -->
  <el-table-column prop="deptCode" label="唯一编码" width="200" />
  
  <!-- 上级公司 -->
  <el-table-column prop="serviceCompanyId" label="上级公司" width="240">
    <template #default="scope">
      <el-tree-select
        v-if="scope.row.serviceCompanyId"
        v-model="scope.row.serviceCompanyId"
        :data="deptList"
        disabled
      />
    </template>
  </el-table-column>
  
  <!-- 排序 -->
  <el-table-column prop="sort" label="排序" width="200" />
  
  <!-- 状态 -->
  <el-table-column prop="status" label="状态" width="200">
    <template #default="{ row }">
      <dict-tag :type="DICT_TYPE.SYSTEM_NORMAL_DISABLE" :value="row.status" />
    </template>
  </el-table-column>
  
  <!-- 创建时间 -->
  <el-table-column label="创建时间" prop="createTime" width="200">
    <template #default="scope">
      <span>{{ parseTime(scope.row.createTime) }}</span>
    </template>
  </el-table-column>
  
  <!-- 数据来源 -->
  <el-table-column prop="dataSource" label="数据来源" width="110" />
  
  <!-- 创建人 -->
  <el-table-column prop="createBy" label="创建人" width="110" />
  
  <!-- 操作 -->
  <el-table-column label="操作" width="250" fixed="right">
    <template #default="scope">
      <el-button v-hasPermi="['system:company:assign']">
        分配
      </el-button>
      <el-button v-hasPermi="['system:company:update']">
        编辑
      </el-button>
      <el-button v-hasPermi="['system:company:delete']">
        删除
      </el-button>
    </template>
  </el-table-column>
</el-table>
```

### 7.2 树形展示逻辑

```typescript
// 树形表格配置
:tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
:default-expand-all="isExpandAll"
row-key="id"
```

**展示效果**:
```
📁 服务公司 A
  └─ 📁 供应商 A1
      ├─ 子供应商 A1-1
      └─ 子供应商 A1-2
  └─ 供应商 A2

📁 服务公司 B
  └─ 供应商 B1
```

### 7.3 上级公司显示优化

```vue
<el-tree-select
  v-if="scope.row.serviceCompanyId"
  v-model="scope.row.serviceCompanyId"
  :data="deptList"
  disabled
  :style="{ '--el-border-color-light': '#ffffff' }"
  suffix-icon="hidden-icon"
/>
```

**设计意图**:
- 使用 tree-select 而不是普通文本
- 可以清晰显示公司在组织树中的位置
- 禁用状态，只用于展示，不可编辑
- 自定义样式，使其看起来像文本而不是输入框

## 八、数据流转图

```
用户操作
   ↓
Vue Component (index.vue)
   ↓
API 调用 (src/api/system/company.ts)
   ↓
后端接口 (/system/company/*)
   ↓
Service 层处理
   ↓
数据库操作
   ↓
返回结果
   ↓
Store 更新（如需要）
   ↓
UI 刷新
```

## 九、关键技术实现

### 9.1 树形数据处理

```typescript
import { handlePhaseTree } from '@/utils/tree'

// 将扁平数据转换为树形结构
const response = await listCompany()
deptOptions.value = handlePhaseTree(response, 'id')
```

**转换示例**:
```typescript
// 输入（扁平数据）
[
  { id: 1, name: '公司A', parentId: null },
  { id: 2, name: '供应商A1', parentId: 1 },
  { id: 3, name: '供应商A2', parentId: 1 },
]

// 输出（树形数据）
[
  {
    id: 1,
    name: '公司A',
    parentId: null,
    children: [
      { id: 2, name: '供应商A1', parentId: 1, children: [] },
      { id: 3, name: '供应商A2', parentId: 1, children: [] }
    ]
  }
]
```

### 9.2 防止循环引用

```typescript
// 排除自身及其子公司
const data = await listCompanyExcludeChild(item.id as number)
```

**后端实现逻辑**:
```sql
-- 查询所有公司，排除指定 ID 及其所有子孙公司
WITH RECURSIVE company_tree AS (
  -- 初始查询：找到指定公司
  SELECT id, parent_id
  FROM company
  WHERE id = #{companyId}
  
  UNION ALL
  
  -- 递归查询：找到所有子孙公司
  SELECT c.id, c.parent_id
  FROM company c
  INNER JOIN company_tree ct ON c.parent_id = ct.id
)
-- 返回不在排除列表中的公司
SELECT *
FROM company
WHERE id NOT IN (SELECT id FROM company_tree)
```

## 十、总结

Company 模块是 OLP 系统的**组织架构基础模块**，核心业务价值在于：

1. **支撑多租户架构**：实现不同公司数据隔离
2. **服务供应商管理**：管理服务公司与供应商的合作关系
3. **权限管理基础**：为用户权限提供组织维度
4. **数据统计维度**：按公司进行培训数据分析

关键业务特点：
- 双类型公司：服务公司和供应商的区分
- 树形结构：支持多级供应商管理
- 合同管理员：实现公司级的权限分配
- 多数据源：支持手动创建、MDS 同步、批量导入

该模块的设计充分考虑了企业培训业务的复杂性和灵活性，是整个系统的核心基础模块之一。

