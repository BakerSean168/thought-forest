---
tags:
  - tech/app/apifox
  - type/howto
  - status/evergreen
description: 将项目API接口导入Apifox(通过OpenAPI/Swagger文档)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Resource]] | [[API开发 MOC]]

---


# 从项目中导入swagger文档 

## 问题详情
希望把项目中的 api 接口都放到 apifox 中
## 解决方案
### 方法一：通过 OpenAPI 文档导入（推荐）

#### 步骤：

1. 启动 API 服务器：`pnpm dev:api`

2. 访问 API 文档：http://localhost:3888/api-docs

3. 获取 OpenAPI JSON：http://localhost:3888/api-docs.json

4. 在 Apifox 中选择"导入" → "OpenAPI/Swagger"

5. 粘贴 JSON 内容或上传文件

#### 自动化导出：

运行以下命令生成文档文件：

```bash

cd apps/api

node src/scripts/export-api-docs.js

```

### 方法二：通过 URL 同步导入

#### 步骤：

1. 在 Apifox 中选择"导入" → "URL 导入"

2. 输入：`http://localhost:3888/api-docs.json`

3. 选择"定时同步"以保持文档更新

### 方法三：手动创建接口

#### 适用场景：

- 需要精确控制接口文档

- 添加自定义测试用例

- 特殊的业务逻辑说明

## 实战经验
## 经验总结
## 信息参考
