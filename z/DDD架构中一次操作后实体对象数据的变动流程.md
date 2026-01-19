---
tags:
  - tech/dev/architecture
  - type/concept
  - status/growing
description: DDD架构中一次操作后实体对象数据的变动流程
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[设计模式 MOC]] | [[后端开发 MOC]]

---


# DDD架构中一次操作后实体对象数据的变动流程，统一接口 

## 问题详情

以 [[「Goal」模块]] 为例：  
聚合根对象 Goal，包含实体 KeyResult、GoalRecord、GoalReview。
DDD架构中一次操作后实体对象数据的变动流程 应该是怎么样的？
- 当新建Goal中同时附带其实体数据
- 当增删查改 Goal 的一个实体，比如 KeyResult
- 当 Goal 的实体变化导致的一些列业务逻辑时
	- 比如，完成一个 任务 时，用户点击加号，添加 GoalRecord，然后根据 GoalRecord，来添加KeyResult 的 currentValue，Goal 的进度也会跟着改变（现在使用临时计算得出）、还有一些统计数据需要跟着改变

所以我现在的初步理解是，首先应该是 聚合根对象主导旗下的其他实体对象，将 KeyResult、GoalRecord 等实体的接口都放在 Goal 下面：  

**增**：
1. 前端生成（使用 `forCreate()` 方法初始化 对象）一个完整的（随机生成uuid），包含旗下实体的 前端聚合根对象 Goal，让用户通过表单编辑
2. 然后再转为 DTO 对象（注意数据格式转换），传输给后端的 CreateGoal 应用层服务函数（聚合根调用，仓储持久化，跨聚合根服务），
3. 函数再调用后端的 Goal 聚合根对象应该有的 CreateGoal 业务方法，通过DTO直接生成后端的 Goal 实体对象，并触发相应的业务逻辑（比如自动添加一些 KeyResult 之类的，可能修改 Goal 对象属性的业务）
4. 函数再调用仓库服务，将 Goal 对象持久化到数据库（postgresql）中（将所有数据展开扁平化存储），要注意数据转换
	1. 数据格式中（Date 转为 TIMESTAMP WITH TIME ZONE，tags 转JSON）

```ts
/**
 * 创建目标请求 DTO
 */
export interface CreateGoalRequest {
  goalUuid: string;
  name: string;
  description?: string;
  color: string;
  dirUuid?: string;
  startTime: number; // timestamp
  endTime: number; // timestamp
  note?: string;
  analysis: {
    motive: string;
    feasibility: string;
    importanceLevel: ImportanceLevel;
    urgencyLevel: UrgencyLevel;
  };
  metadata: {
    tags: string[];
    category: string;
  };
  keyResults?: CreateKeyResultRequest[];
  records?: CreateGoalRecordRequest[];
  reviews?: CreateGoalReviewRequest[];
}
```

**查 (单资源)**：
1. 前端传 id
2. 后端有 getGoalByGoalUuid 方法
3. 从仓储层恢复成实体对象（完整，包含所有旗下的实体），然后计算一些前端特有的用于展示的属性
4. 再生成 GetGoalResponse DTO 对象返回
5. 前端 再将 DTO 转换成完整的 client 下的 Goal 实体对象

**查 (多资源)**
1. 同上，只是传输的是 page、size、startTime 等筛选条件

**改 (增量更新)**
1. 给前端实体对象增加跟踪修改，
2. 应该只提供一次修改一个对象的入口（比如只修改 Goal（属性），或者只修改 KeyResult，或者只修改 GoalRecord），即只有属性修改，不存在增删
3. 后端服务层应该有 updateGoal、updateKeyResultInGoal 之类的函数调用 Goal 实体的内部业务方法来触发修改的属性，对于 color、name 等属性，直接更新即可，对于 部分业务数据则应该单独处理。
4. 然后调用 Goal 仓储的更新 Goal 实体的增量更新函数来统一管理

**改 (全量更新)**：
1. 修改KeyResult、GoalRecord 等后，直接把整个 Goal 实体发送到后端，全量修改

**删 (单资源)**：
1. 删除 goal，直接传入 goal 的 uuid；删除旗下 实体，则传入 goalUuid + 实体 的 uuid

**删 (批量)**：
1. 删除 goal，直接传入 goal 的 uuid 字符串；删除旗下 实体，则传入 goalUuid + 实体 的 uuid 字符串

### 关键修改

1. 聚合根需要 accountUuid 属性，其他实体不需要 accountUuid，通过 聚合根 uuid 关联
2. 接口 DTO 的属性
3. 服务端实体内部实现 toDatabase、fromDatebase、toDTO、fromDTO 方法（数据扁平化+类型转换为方便存储的类型），
4. 数据格式变化： clientEntity - DTO - serverEntity - Database
5. 客户端实体对象应该要有 toDTO、fromDTO 方法

### 期望

得到一个通用的流程来指导其他模块的代码

## 解决方案

## 实战经验

## 经验总结

## 信息参考
