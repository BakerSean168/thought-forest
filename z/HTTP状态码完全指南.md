---
tags:
  - tech/dev/backend
  - tech/http
  - type/howto
  - status/evergreen
description: HTTP 状态码详解，包含 1xx/2xx/3xx/4xx/5xx 所有分类及用法
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[计算机网络 MOC]] | [[RESTful-API]] | [[API 通信技术]]

---

# HTTP 状态码完全指南

> HTTP 状态码是服务器对客户端请求的响应结果的标准化表示。  
> 共分为 5 类：1xx（信息）、2xx（成功）、3xx（重定向）、4xx（客户端错误）、5xx（服务器错误）

---

## 📊 状态码分类概览

| 分类 | 说明 | 常见代码 |
|------|------|---------|
| **1xx** | 信息响应 | 100, 101 |
| **2xx** | 成功响应 | 200, 201, 204, 206 |
| **3xx** | 重定向 | 301, 302, 304, 307, 308 |
| **4xx** | 客户端错误 | 400, 401, 403, 404, 409, 429 |
| **5xx** | 服务器错误 | 500, 501, 502, 503, 504 |

---

## 1️⃣ 1xx - 信息响应

> 临时响应，表示请求已被接收，继续处理

### 100 Continue
- **含义**：继续上传
- **场景**：客户端发送 `Expect: 100-continue` 头，服务器允许继续上传
- **用法**：大文件上传前的预检

```http
POST /upload HTTP/1.1
Expect: 100-continue

HTTP/1.1 100 Continue
```

### 101 Switching Protocols
- **含义**：协议切换
- **场景**：WebSocket 升级、HTTP/2 升级
- **用法**：
```http
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade

HTTP/1.1 101 Switching Protocols
```

---

## 2️⃣ 2xx - 成功响应

> 请求成功接收、理解且处理

### 200 OK ⭐⭐⭐⭐⭐
- **含义**：请求成功
- **场景**：最常见，GET/POST/PUT/PATCH/DELETE 成功返回
- **响应体**：有（通常返回数据）
- **缓存**：可缓存

```http
GET /api/users/1 HTTP/1.1

HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "name": "Alice"}
```

### 201 Created ⭐⭐⭐⭐
- **含义**：资源创建成功
- **场景**：POST 请求成功创建新资源
- **特点**：应包含 `Location` 头指向新资源
- **响应体**：有（新创建的资源）

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{"name": "Bob"}

HTTP/1.1 201 Created
Location: /api/users/2
Content-Type: application/json

{"id": 2, "name": "Bob"}
```

### 202 Accepted
- **含义**：请求已接受但尚未处理
- **场景**：异步处理任务（后台任务、定时任务）
- **用法**：返回任务ID供后续查询

```http
POST /api/export HTTP/1.1

HTTP/1.1 202 Accepted
Location: /api/tasks/task-123

{"task_id": "task-123", "status": "processing"}
```

### 204 No Content ⭐⭐⭐⭐
- **含义**：请求成功但无返回内容
- **场景**：DELETE 成功、PUT 成功但无返回数据
- **响应体**：无
- **特点**：比 200 更准确语义

```http
DELETE /api/users/1 HTTP/1.1

HTTP/1.1 204 No Content
```

### 206 Partial Content
- **含义**：返回部分内容
- **场景**：断点下载、视频播放进度定位
- **头部**：`Content-Range`, `Content-Length`

```http
GET /video.mp4 HTTP/1.1
Range: bytes=0-1023

HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/10485760
Content-Length: 1024
```

---

## 3️⃣ 3xx - 重定向

> 表示完成请求需要进一步操作

### 301 Moved Permanently ⭐⭐⭐⭐
- **含义**：永久重定向
- **场景**：旧 URL 永久废弃，移到新 URL
- **缓存**：可被浏览器和搜索引擎缓存
- **GET 转换**：客户端自动跟随 GET 请求

```http
GET /old-path HTTP/1.1

HTTP/1.1 301 Moved Permanently
Location: /new-path
```

**示例**：网站域名变更 `example.com` → `newexample.com`

### 302 Found（临时重定向）⭐⭐⭐⭐
- **含义**：临时重定向
- **场景**：临时页面转移、会话转移
- **缓存**：不缓存（除非显式指定）
- **特点**：原方法保留（GET 仍为 GET）

```http
POST /login HTTP/1.1
Content-Type: application/x-www-form-urlencoded

username=alice&password=pass123

HTTP/1.1 302 Found
Location: /dashboard
```

**vs 301**：
- 301：永久，搜索引擎更新索引
- 302：临时，搜索引擎保留旧 URL

### 303 See Other
- **含义**：查看其他资源
- **场景**：POST-Redirect-GET 模式（防止表单重复提交）
- **特点**：强制改为 GET 请求

```http
POST /api/users HTTP/1.1

HTTP/1.1 303 See Other
Location: /api/users/2
```

客户端会自动 `GET /api/users/2`

### 304 Not Modified ⭐⭐⭐⭐
- **含义**：资源未修改，可用缓存
- **场景**：条件请求（If-None-Match / If-Modified-Since）
- **响应体**：无
- **缓存**：使用本地缓存版本

```http
GET /api/data HTTP/1.1
If-None-Match: "abc123"

HTTP/1.1 304 Not Modified
```

浏览器使用本地缓存，节省带宽

### 307 Temporary Redirect
- **含义**：临时重定向（严格方法保留）
- **场景**：POST 重定向仍为 POST
- **vs 302**：302 可能将 POST 转换为 GET（历史原因）

```http
POST /api/upload HTTP/1.1

HTTP/1.1 307 Temporary Redirect
Location: /new-api/upload
```

客户端仍发送 POST 到新地址

### 308 Permanent Redirect
- **含义**：永久重定向（严格方法保留）
- **场景**：API 迁移（POST/PUT/DELETE 保持原方法）
- **vs 301**：301 可能改变方法，308 保留方法

```http
POST /api/v1/data HTTP/1.1

HTTP/1.1 308 Permanent Redirect
Location: /api/v2/data
```

---

## 4️⃣ 4xx - 客户端错误

> 请求有问题，客户端需要修正

### 400 Bad Request ⭐⭐⭐⭐
- **含义**：请求格式错误
- **场景**：
  - JSON 格式不正确
  - 参数类型错误
  - 必需参数缺失
- **调试**：检查请求头、请求体语法

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{invalid json}

HTTP/1.1 400 Bad Request
Content-Type: application/json

{"error": "Invalid JSON format"}
```

### 401 Unauthorized ⭐⭐⭐⭐
- **含义**：未认证（缺少身份验证凭证）
- **场景**：
  - 没有发送 Authorization 头
  - Token 过期
  - Token 无效
- **解决**：提供有效的认证凭证

```http
GET /api/private HTTP/1.1

HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="api"

{"error": "Missing or invalid token"}
```

**vs 403**：
- 401：没有身份验证 / 认证失败
- 403：有身份验证但没有权限

### 403 Forbidden ⭐⭐⭐⭐
- **含义**：禁止访问（已认证但无权限）
- **场景**：
  - 用户没有访问权限
  - 资源受限
  - IP 被屏蔽
- **特点**：不返回 WWW-Authenticate 头

```http
GET /api/admin/users HTTP/1.1
Authorization: Bearer token123

HTTP/1.1 403 Forbidden

{"error": "Insufficient permissions"}
```

### 404 Not Found ⭐⭐⭐⭐⭐
- **含义**：资源不存在
- **场景**：
  - 请求的 URL 路径不存在
  - 资源已删除
  - 拼写错误
- **最常见的状态码**

```http
GET /api/users/99999 HTTP/1.1

HTTP/1.1 404 Not Found
Content-Type: application/json

{"error": "User not found"}
```

### 405 Method Not Allowed
- **含义**：HTTP 方法不允许
- **场景**：用错了 HTTP 方法（如 POST 到 GET 接口）
- **头部**：应包含 `Allow` 列出允许的方法

```http
POST /api/users/1 HTTP/1.1

HTTP/1.1 405 Method Not Allowed
Allow: GET, PUT, DELETE

{"error": "Method POST not allowed"}
```

### 409 Conflict ⭐⭐⭐
- **含义**：请求冲突
- **场景**：
  - 资源重复（如注册相同邮箱）
  - 版本冲突
  - 并发修改冲突

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{"email": "alice@example.com"}

HTTP/1.1 409 Conflict

{"error": "Email already exists"}
```

### 410 Gone
- **含义**：资源已删除且不会返回
- **场景**：永久删除的资源（vs 404 可能是临时）
- **特点**：SEO 中告诉搜索引擎资源已永久删除

```http
GET /api/deprecated-endpoint HTTP/1.1

HTTP/1.1 410 Gone
```

### 422 Unprocessable Entity
- **含义**：验证失败（格式正确但逻辑错误）
- **场景**：
  - 邮箱格式不正确
  - 年龄超出范围
  - 业务逻辑验证失败

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{"email": "invalid-email", "age": 200}

HTTP/1.1 422 Unprocessable Entity

{"errors": {"email": "Invalid format", "age": "Must be < 150"}}
```

### 429 Too Many Requests ⭐⭐⭐⭐
- **含义**：请求过于频繁（限流）
- **场景**：用户请求超过频率限制
- **头部**：
  - `Retry-After`：建议重试等待时间
  - `X-RateLimit-Limit`：限额
  - `X-RateLimit-Remaining`：剩余配额

```http
GET /api/data HTTP/1.1

HTTP/1.1 429 Too Many Requests
Retry-After: 60
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0

{"error": "Rate limit exceeded"}
```

---

## 5️⃣ 5xx - 服务器错误

> 服务器出错，无法完成请求

### 500 Internal Server Error ⭐⭐⭐⭐⭐
- **含义**：服务器内部错误
- **场景**：
  - 未捕获的异常
  - 数据库连接失败
  - 代码 Bug
- **调试**：查看服务器日志

```http
GET /api/users HTTP/1.1

HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{"error": "Internal server error", "timestamp": "2025-12-08T10:00:00Z"}
```

### 501 Not Implemented
- **含义**：功能未实现
- **场景**：
  - API 功能开发中
  - 某个 HTTP 方法未支持
- **特点**：比 405 更表明功能根本不存在

```http
POST /api/beta-feature HTTP/1.1

HTTP/1.1 501 Not Implemented

{"error": "Feature not yet implemented"}
```

### 502 Bad Gateway
- **含义**：网关错误（代理服务器收到错误响应）
- **场景**：
  - Nginx 后的应用服务器宕机
  - 应用服务器无响应
  - 代理服务器与后端通信失败

```http
GET / HTTP/1.1

HTTP/1.1 502 Bad Gateway

<html><body><h1>502 Bad Gateway</h1></body></html>
```

**常见原因**：
- 应用服务器未启动
- 应用服务器超时

### 503 Service Unavailable ⭐⭐⭐
- **含义**：服务不可用
- **场景**：
  - 服务器维护
  - 临时故障
  - 依赖服务不可用（数据库）
- **头部**：应包含 `Retry-After`

```http
GET / HTTP/1.1

HTTP/1.1 503 Service Unavailable
Retry-After: 3600

{"error": "Service under maintenance"}
```

### 504 Gateway Timeout
- **含义**：网关超时
- **场景**：
  - 后端服务响应超时
  - 代理服务器等待时间过长
  - 网络延迟

```http
GET /api/slow-operation HTTP/1.1

HTTP/1.1 504 Gateway Timeout

{"error": "Request timeout"}
```

---

## 🎯 常用搭配与最佳实践

### REST API 设计推荐

| 操作 | 推荐状态码 | 备选 |
|------|----------|------|
| 创建资源成功 | **201** | 200 |
| 查询存在 | **200** | - |
| 查询不存在 | **404** | - |
| 删除成功 | **204** | 200 |
| 更新成功 | **200 / 204** | 201 |
| 参数错误 | **400** | 422 |
| 认证失败 | **401** | - |
| 权限不足 | **403** | - |
| 重复资源 | **409** | 400 |
| 限流 | **429** | - |
| 服务器错误 | **500** | - |

### 响应体设计

**成功响应** (2xx)：
```json
{
  "data": {...},
  "timestamp": "2025-12-08T10:00:00Z"
}
```

**客户端错误** (4xx)：
```json
{
  "error": "描述性错误信息",
  "code": "ERROR_CODE",
  "details": {...}
}
```

**服务器错误** (5xx)：
```json
{
  "error": "Internal server error",
  "request_id": "req-123",
  "timestamp": "2025-12-08T10:00:00Z"
}
```

---

## 🔍 状态码速查表

### 最常用 Top 10

| 状态码 | 名称 | 使用频率 |
|--------|------|---------|
| 200 | OK | ⭐⭐⭐⭐⭐ |
| 201 | Created | ⭐⭐⭐⭐ |
| 204 | No Content | ⭐⭐⭐⭐ |
| 301 | Moved Permanently | ⭐⭐⭐ |
| 302 | Found | ⭐⭐⭐ |
| 304 | Not Modified | ⭐⭐⭐⭐ |
| 400 | Bad Request | ⭐⭐⭐⭐ |
| 401 | Unauthorized | ⭐⭐⭐⭐ |
| 403 | Forbidden | ⭐⭐⭐⭐ |
| 404 | Not Found | ⭐⭐⭐⭐⭐ |
| 429 | Too Many Requests | ⭐⭐⭐⭐ |
| 500 | Internal Server Error | ⭐⭐⭐⭐ |

---

## 💡 常见错误与解决

| 问题 | 原因 | 解决 |
|------|------|------|
| 总是 404 | URL 拼写错误 / 资源不存在 | 检查 URL、确认资源存在 |
| 总是 401 | 没有发送 token / token 过期 | 检查 Authorization 头、刷新 token |
| 总是 500 | 服务器代码 Bug | 查看服务器日志，定位异常 |
| 收到 502 | 后端服务宕机 | 检查应用服务器、重启服务 |
| 收到 503 | 服务维护或过载 | 等待或减少请求频率 |

---

> **总结**：  
> - 2xx = ✅ 成功  
> - 3xx = 🔄 转向  
> - 4xx = ⚠️ 客户端问题  
> - 5xx = 🔥 服务器问题

---

**上级索引**：[[计算机网络 MOC]] | [[RESTful-API]] | [[API 通信技术]]
