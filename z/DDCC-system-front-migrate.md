---
tags:
  - tech/dev/frame
  - type/journal
  - status/seed
description: DDCC项目前端迁移问题记录
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DDCC]] | [[项目问题汇总]]

---


# DDCC-system-front-migrate 

## 问题详情

- [x] 添加用户报错
- [x] 更新用户涉及直属管理员有问题
- [ ] 角色数据权限未迁移：需跟 OLP 一样支持子部门数据权限
- [x] 更新部门信息赋值有问题：请看好文档
- [x] 新增部门按钮无反应：传参估计有问题，需看好文档
- [x] 岗位管理进入即报错：导致后续功能无法测试
- [x] 公司与子部门管理缺失：未看到相关功能及路由，确认是否遗漏或未提交代码


## 解决方案

### 部门

添加默认的 companyId，修改参数名字

### 岗位

添加 panel 依赖

### 子部门

dict

### 字典迁移

  // 用户模块
  // olp一期用户性别字典跟芋道的键值不一样
  SYSTEM_PhaseI_USER_SEX = 'sys_user_sex',
  SYSTEM_USER_STATUS = 'sys_user_status',
  SYSTEM_USER_WORK_TYPE = 'sys_user_work_type',
  SYSTEM_NATIONALITY = 'sys_nationality',
  SYSTEM_USER_WORK_TERMS = 'sys_user_work_terms',
  SYSTEM_USER_USER_STATUS = 'sys_user_user_status',

## userFrom表单获取不到 dept

![[Pasted image 20251126150204.png]]
## 实战经验
## 经验总结
## 信息参考
