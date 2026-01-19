---
tags:
  - tech/dev/backend
  - type/concept
  - status/evergreen
description: API 版本管理策略 - URL、Header、Media Type 方案对比
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[RESTful-API]]

---

# API 版本管理

> 如何优雅地管理 API 版本变更，确保向后兼容性

---

## 🎯 为什么需要版本管理？

### 常见场景

1. **破坏性变更** - 修改字段名、类型
2. **废弃功能** - 移除旧端点
3. **新增功能** - 添加新字段或端点
4. **重构** - 改变资源结构

### 不做版本管理的后果

```typescript
// ❌ 直接修改现有 API
// Before
GET /api/users/1
{
  "name": "John"
}

// After（破坏性变更）
GET /api/users/1
{
  "firstName": "John",  // name 字段被拆分
  "lastName": "Doe"
}

// 结果：所有旧客户端都会崩溃！
```

---

## 📊 版本管理策略对比

| 策略 | 示例 | 优点 | 缺点 | 推荐度 |
|------|------|------|------|--------|
| **URL 路径** | `/api/v1/users` | 简单明了 | URL 冗长 | ⭐⭐⭐⭐⭐ |
| **查询参数** | `/api/users?version=1` | 灵活 | 容易被忽略 | ⭐⭐ |
| **Header** | `Accept: application/vnd.api.v1+json` | URL 干净 | 不直观 | ⭐⭐⭐ |
| **Media Type** | `Content-Type: application/vnd.api.v1+json` | 符合 REST | 复杂 | ⭐⭐⭐ |
| **子域名** | `v1.api.example.com` | 独立部署 | 运维复杂 | ⭐⭐ |

---

## 🏗️ 方案详解

### 1. URL 路径版本（推荐）

**最常用、最直观的方案**

```typescript
// 版本 1
app.get('/api/v1/users/:id', (req, res) => {
  res.json({ name: user.name });
});

// 版本 2（破坏性变更）
app.get('/api/v2/users/:id', (req, res) => {
  res.json({
    firstName: user.firstName,
    lastName: user.lastName
  });
});

// 版本 3（新增字段）
app.get('/api/v3/users/:id', (req, res) => {
  res.json({
    firstName: user.firstName,
    lastName: user.lastName,
    avatar: user.avatar  // 新增
  });
});
```

**优点**：
- ✅ 一目了然，容易测试
- ✅ 可以同时部署多个版本
- ✅ 文档清晰
- ✅ 浏览器直接访问

**缺点**：
- ❌ URL 变长
- ❌ 版本号在 URL 中显得"不够 RESTful"

**适用场景**：
- 大多数公开 API
- 需要长期支持多个版本
- 面向第三方开发者

---

### 2. Header 版本

使用自定义 Header 或 Accept Header：

```typescript
// 方案 A: 自定义 Header
app.get('/api/users/:id', (req, res) => {
  const version = req.headers['api-version'] || '1';
  
  if (version === '1') {
    res.json({ name: user.name });
  } else if (version === '2') {
    res.json({
      firstName: user.firstName,
      lastName: user.lastName
    });
  }
});

// 方案 B: Accept Header
app.get('/api/users/:id', (req, res) => {
  const accept = req.headers.accept;
  
  if (accept.includes('application/vnd.api.v2+json')) {
    res.json({ firstName: user.firstName, lastName: user.lastName });
  } else {
    res.json({ name: user.name });
  }
});
```

**客户端使用**：

```typescript
// 方案 A
fetch('/api/users/1', {
  headers: {
    'API-Version': '2'
  }
});

// 方案 B
fetch('/api/users/1', {
  headers: {
    'Accept': 'application/vnd.api.v2+json'
  }
});
```

**优点**：
- ✅ URL 保持简洁
- ✅ 符合 HTTP 规范（Accept Header）

**缺点**：
- ❌ 不直观，难以测试
- ❌ 浏览器无法直接访问不同版本
- ❌ 缓存策略复杂

**适用场景**：
- 内部 API
- 版本差异较小
- 追求 URL 简洁性

---

### 3. 查询参数版本

```typescript
app.get('/api/users/:id', (req, res) => {
  const version = req.query.version || '1';
  
  if (version === '1') {
    res.json({ name: user.name });
  } else if (version === '2') {
    res.json({
      firstName: user.firstName,
      lastName: user.lastName
    });
  }
});
```

**客户端使用**：

```typescript
fetch('/api/users/1?version=2');
```

**优点**：
- ✅ 灵活，可选

**缺点**：
- ❌ 容易被忘记
- ❌ 与其他查询参数混淆
- ❌ 缓存策略复杂

**适用场景**：
- ❌ 不推荐（容易出错）

---

## 🎯 DailyUse 推荐方案

### 使用 URL 路径版本

```
/api/v1/users
/api/v1/sync/push
/api/v1/devices
```

### 版本策略

1. **主版本号（Major）** - 破坏性变更
   - 修改字段名
   - 删除字段
   - 改变响应结构

2. **不需要次版本号（Minor）**
   - 只添加新字段 → 无需新版本（向后兼容）
   - 添加新端点 → 无需新版本

### 实现方案

```typescript
// src/routes/index.ts
import express from 'express';
import v1Routes from './v1';
import v2Routes from './v2';

const router = express.Router();

// 版本 1
router.use('/v1', v1Routes);

// 版本 2
router.use('/v2', v2Routes);

// 默认版本（指向最新稳定版）
router.use('/', v1Routes);

export default router;
```

```typescript
// src/routes/v1/index.ts
import express from 'express';
import usersRouter from './users';
import syncRouter from './sync';

const router = express.Router();

router.use('/users', usersRouter);
router.use('/sync', syncRouter);

export default router;
```

```typescript
// src/routes/v2/index.ts
import express from 'express';
import usersRouter from './users';
import syncRouter from '../v1/sync';  // 复用 v1 的同步路由

const router = express.Router();

router.use('/users', usersRouter);  // 使用新版用户路由
router.use('/sync', syncRouter);    // 复用旧版同步路由

export default router;
```

---

## 📝 版本迁移指南

### 1. 废弃通知

```typescript
// v1 路由中添加废弃警告
app.get('/api/v1/users/:id', (req, res) => {
  res.set('Deprecation', 'true');
  res.set('Sunset', 'Sat, 31 Dec 2025 23:59:59 GMT');
  res.set('Link', '</api/v2/users>; rel="alternate"');
  
  res.json({ name: user.name });
});
```

### 2. 客户端检测

```typescript
const response = await fetch('/api/v1/users/1');

if (response.headers.get('Deprecation') === 'true') {
  const sunset = response.headers.get('Sunset');
  console.warn(`API 已废弃，将于 ${sunset} 停止服务`);
  
  const newVersion = response.headers.get('Link');
  console.info(`请迁移到: ${newVersion}`);
}
```

### 3. 版本映射层

```typescript
// 为旧版本提供兼容层
class UserAdapter {
  // v2 → v1 适配
  static toV1(v2User: UserV2): UserV1 {
    return {
      name: `${v2User.firstName} ${v2User.lastName}`
    };
  }
  
  // v1 → v2 适配
  static toV2(v1User: UserV1): UserV2 {
    const [firstName, lastName] = v1User.name.split(' ');
    return { firstName, lastName };
  }
}

// v1 路由使用适配器
app.get('/api/v1/users/:id', async (req, res) => {
  const v2User = await getUserV2(req.params.id);
  const v1User = UserAdapter.toV1(v2User);
  res.json(v1User);
});
```

---

## 🔄 版本共存策略

### 场景：v1 和 v2 同时运行

```
┌─────────────────────────────────────────────────┐
│                   API Gateway                   │
├─────────────────────────────────────────────────┤
│                                                 │
│   /api/v1/*  ──►  Service v1 (旧逻辑)           │
│   /api/v2/*  ──►  Service v2 (新逻辑)           │
│                                                 │
│   80% 流量   ──►  v1                            │
│   20% 流量   ──►  v2 (灰度发布)                 │
│                                                 │
└─────────────────────────────────────────────────┘
```

### 共享数据库方案

```typescript
// 数据库层统一使用 v2 结构
class UserRepository {
  async findById(id: string): Promise<UserV2> {
    return db.users.findOne({ id });
  }
}

// v1 路由适配
app.get('/api/v1/users/:id', async (req, res) => {
  const user = await userRepo.findById(req.params.id);
  res.json(UserAdapter.toV1(user));
});

// v2 路由直接返回
app.get('/api/v2/users/:id', async (req, res) => {
  const user = await userRepo.findById(req.params.id);
  res.json(user);
});
```

---

## 🎯 最佳实践

### 1. 语义化版本号

```
v1.0.0
 │ │ │
 │ │ └─ Patch（补丁）- 仅修复 bug，完全兼容
 │ └─── Minor（次版本）- 新增功能，向后兼容
 └───── Major（主版本）- 破坏性变更
```

**API 版本只需要主版本号**：
- `/api/v1/` - 主版本 1
- `/api/v2/` - 主版本 2

### 2. 默认版本指向最新稳定版

```typescript
// ❌ 不推荐：默认到最新
app.use('/api', latestRouter);  // 可能导致旧客户端崩溃

// ✅ 推荐：显式声明版本
app.use('/api/v1', v1Router);
app.use('/api/v2', v2Router);
```

### 3. 至少支持 2 个版本

```
当前稳定版: v2
上一个版本: v1 (废弃中，6个月后停止)
下一个版本: v3 (beta)
```

### 4. 文档清晰标注

```yaml
# OpenAPI 规范
servers:
  - url: https://api.dailyuse.app/v1
    description: Stable (Deprecated, Sunset 2025-12-31)
  - url: https://api.dailyuse.app/v2
    description: Stable (Current)
  - url: https://api.dailyuse.app/v3
    description: Beta
```

### 5. 自动化测试

```typescript
describe('API Versioning', () => {
  it('v1 and v2 should return different formats', async () => {
    const v1Response = await request(app).get('/api/v1/users/1');
    expect(v1Response.body).toHaveProperty('name');
    
    const v2Response = await request(app).get('/api/v2/users/1');
    expect(v2Response.body).toHaveProperty('firstName');
    expect(v2Response.body).toHaveProperty('lastName');
  });
  
  it('deprecated v1 should include deprecation headers', async () => {
    const response = await request(app).get('/api/v1/users/1');
    expect(response.headers.deprecation).toBe('true');
    expect(response.headers.sunset).toBeDefined();
  });
});
```

---

## 🔗 相关资源

- [[RESTful-API]] - REST API 设计规范
- [[OpenAPI 规范详解]] - OpenAPI 版本管理
- [[API 通信技术]] - API 技术对比

---

## 💡 常见问题

### Q: 什么时候需要升级主版本？

**破坏性变更**：
- 修改字段名：`name` → `firstName`
- 删除字段：移除 `age`
- 改变类型：`id: string` → `id: number`
- 改变响应结构：`{ user }` → `{ data: { user } }`

**不需要升级**（向后兼容）：
- 添加新字段：`{ name, email, avatar }`（新增 avatar）
- 添加新端点：`GET /api/v1/users/stats`
- 修复 bug

### Q: 如何处理客户端升级？

**强制升级**：
```typescript
app.get('/api/v1/*', (req, res) => {
  res.status(410).json({
    error: 'version_no_longer_supported',
    message: 'API v1 is no longer supported. Please upgrade to v2.',
    upgradeUrl: 'https://docs.api.com/migration-guide'
  });
});
```

**软性提示**：
```typescript
app.use('/api/v1', (req, res, next) => {
  res.set('Deprecation', 'true');
  res.set('Sunset', 'Sat, 31 Dec 2025 23:59:59 GMT');
  next();
});
```

### Q: 移动应用如何管理版本？

**客户端版本 ≠ API 版本**：

```typescript
// 客户端在请求头中声明自己的版本
fetch('/api/v2/users/1', {
  headers: {
    'X-Client-Version': '1.5.0',
    'X-Platform': 'iOS'
  }
});

// 服务端可以根据客户端版本做兼容处理
if (clientVersion < '2.0.0') {
  // 返回兼容格式
}
```
