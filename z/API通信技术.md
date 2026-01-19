---
tags:
  - tech/dev/backend
  - type/concept
  - status/growing
description: 后端 API 通信技术 - REST/GraphQL/gRPC 全面对比
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[计算机网络 MOC]]

---

# API 通信技术

> 后端 API 通信的三大主流技术：REST、GraphQL、gRPC，各有优劣，适用于不同场景。

---

## 📊 技术对比

| 特性 | REST | GraphQL | gRPC |
|------|------|---------|------|
| **协议** | HTTP/1.1 | HTTP/1.1 | HTTP/2 |
| **数据格式** | JSON/XML | JSON | Protocol Buffers |
| **类型系统** | 无/OpenAPI | 强类型 Schema | 强类型 Proto |
| **请求方式** | 多端点 | 单端点 | 单端点 |
| **实时通信** | 需额外实现 | Subscriptions | 双向流 |
| **性能** | 中等 | 中等 | 高 |
| **浏览器支持** | ✅ 原生 | ✅ 原生 | ⚠️ 需要代理 |
| **学习曲线** | 低 | 中 | 高 |

---

## 🔧 技术详解

### 1. REST (Representational State Transfer)

最传统的 API 设计风格，基于资源和 HTTP 动词：

```
GET    /api/users      # 获取用户列表
GET    /api/users/1    # 获取单个用户
POST   /api/users      # 创建用户
PUT    /api/users/1    # 更新用户
DELETE /api/users/1    # 删除用户
```

**优点**：
- 简单直观，易于理解
- HTTP 原生支持
- 缓存友好

**缺点**：
- 过度获取/获取不足问题
- 多个资源需要多次请求
- 版本管理困难

详见：[[RESTful API 设计]]

---

### 2. GraphQL

Facebook 开发的查询语言，客户端精确指定需要的数据：

```graphql
# 查询
query {
  user(id: 1) {
    name
    email
    posts {
      title
    }
  }
}

# 变更
mutation {
  createUser(name: "John", email: "john@example.com") {
    id
    name
  }
}

# 订阅
subscription {
  newMessage {
    content
    sender
  }
}
```

**优点**：
- 精确获取所需数据
- 单次请求获取多个资源
- 强类型 Schema
- 实时订阅支持

**缺点**：
- 学习曲线较陡
- 缓存复杂
- 文件上传需要额外处理

详见：
- [[GraphQL-Schema]] - Schema 定义
- [[GraphQL-Subscriptions]] - 实时订阅

---

### 3. gRPC

Google 开发的高性能 RPC 框架：

```protobuf
// 定义服务
service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc ListUsers(ListUsersRequest) returns (stream User);
  rpc CreateUser(CreateUserRequest) returns (User);
}

// 定义消息
message User {
  int32 id = 1;
  string name = 2;
  string email = 3;
}
```

**优点**：
- 高性能（Protocol Buffers）
- 双向流支持
- 强类型
- 多语言支持

**缺点**：
- 浏览器不直接支持
- 调试困难
- 学习曲线陡峭

详见：
- [[gRPC]] - gRPC 基础
- [[gRPC-Protobuf]] - Protocol Buffers

---

## 🎯 选型建议

| 场景 | 推荐技术 |
|------|----------|
| 简单 CRUD 应用 | REST |
| 移动端/复杂数据需求 | GraphQL |
| 微服务间通信 | gRPC |
| 实时应用 | GraphQL Subscriptions / gRPC Streaming |
| 公开 API | REST |
| 内部服务 | gRPC |

---

## 🔗 相关笔记

### REST
- [[RESTful API 设计]]
- [[OpenAPI_Swagger]]

### GraphQL
- [[GraphQL-Schema]]
- [[GraphQL-Subscriptions]]

### gRPC
- [[gRPC]]
- [[gRPC-Protobuf]]

### 前端调用
- [[前端 HTTP 请求]]
- [[axios]]
- [[Fetch API]]

---

> [!tip] 使用 Dataview 查看所有 API 通信相关笔记
> ```dataview
> LIST
> FROM #tech/lang/graphql OR #tech/lang/grpc
> SORT file.name ASC
> ```
