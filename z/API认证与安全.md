---
tags:
  - tech/dev/backend
  - type/concept
  - status/evergreen
description: API 认证与安全 - JWT、OAuth2、API Key 全方案对比
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[RESTful-API]]

---

# API 认证与安全

> API 安全是后端开发的基石，选择合适的认证方案至关重要

---

## 🔐 认证方案对比

| 方案 | 适用场景 | 安全性 | 复杂度 | 推荐度 |
|------|---------|-------|--------|--------|
| **Basic Auth** | 内部工具、简单API | ⭐⭐ | ⭐ | ⭐⭐ |
| **API Key** | 第三方集成、服务间调用 | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| **JWT** | 单页应用、移动应用 | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **OAuth 2.0** | 第三方登录、授权 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Session Cookie** | 传统 Web 应用 | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |

---

## 1️⃣ JWT (JSON Web Token) - 推荐方案

### 工作原理

```
┌──────────────────────────────────────────────────────────────────┐
│                      JWT 认证流程                                 │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   1. 登录请求                                                     │
│   ┌─────────┐                           ┌──────────┐            │
│   │ Client  │  POST /auth/login          │  Server  │            │
│   │         │  { username, password }    │          │            │
│   │         │ ───────────────────────►   │          │            │
│   └─────────┘                           └──────────┘            │
│                                              │                   │
│                                              ├─ 验证用户          │
│                                              ├─ 生成 JWT         │
│                                              ↓                   │
│   ┌─────────┐                           ┌──────────┐            │
│   │         │  { token: "eyJhbG..." }    │          │            │
│   │         │ ◄───────────────────────   │          │            │
│   └─────────┘                           └──────────┘            │
│                                                                  │
│   2. 后续请求（携带 JWT）                                         │
│   ┌─────────┐                           ┌──────────┐            │
│   │         │  GET /api/users/me         │          │            │
│   │         │  Authorization: Bearer ... │          │            │
│   │         │ ───────────────────────►   │          │            │
│   └─────────┘                           └──────────┘            │
│                                              │                   │
│                                              ├─ 验证 JWT 签名    │
│                                              ├─ 检查过期时间      │
│                                              ├─ 提取用户信息      │
│                                              ↓                   │
│   ┌─────────┐                           ┌──────────┐            │
│   │         │  { id: 1, name: "John" }   │          │            │
│   │         │ ◄───────────────────────   │          │            │
│   └─────────┘                           └──────────┘            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### JWT 结构

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
│                                       │                                                                                                   │
│          Header                        │                                    Payload                                                       │                 Signature
│  { "alg": "HS256", "typ": "JWT" }     │  { "sub": "1234567890", "name": "John Doe", "iat": 1516239022 }                                  │  HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

### 实现示例

```typescript
// src/auth/jwt.service.ts
import jwt from 'jsonwebtoken';

export class JwtService {
  private readonly secret = process.env.JWT_SECRET!;
  private readonly expiresIn = '7d';
  
  // 生成 JWT
  sign(payload: { userId: string; email: string }): string {
    return jwt.sign(payload, this.secret, {
      expiresIn: this.expiresIn,
      issuer: 'dailyuse-api',
      audience: 'dailyuse-client'
    });
  }
  
  // 验证 JWT
  verify(token: string): { userId: string; email: string } {
    try {
      return jwt.verify(token, this.secret, {
        issuer: 'dailyuse-api',
        audience: 'dailyuse-client'
      }) as any;
    } catch (error) {
      if (error instanceof jwt.TokenExpiredError) {
        throw new Error('Token has expired');
      }
      if (error instanceof jwt.JsonWebTokenError) {
        throw new Error('Invalid token');
      }
      throw error;
    }
  }
  
  // 刷新 Token
  refresh(oldToken: string): string {
    const payload = this.verify(oldToken);
    return this.sign({ userId: payload.userId, email: payload.email });
  }
}
```

```typescript
// src/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { JwtService } from '../auth/jwt.service';

const jwtService = new JwtService();

export function authMiddleware(req: Request, res: Response, next: NextFunction) {
  try {
    // 从 Authorization header 提取 token
    const authHeader = req.headers.authorization;
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      return res.status(401).json({
        error: 'unauthorized',
        message: 'Missing or invalid authorization header'
      });
    }
    
    const token = authHeader.substring(7); // 移除 "Bearer "
    
    // 验证 token
    const payload = jwtService.verify(token);
    
    // 将用户信息附加到 request
    req.user = payload;
    
    next();
  } catch (error: any) {
    return res.status(401).json({
      error: 'unauthorized',
      message: error.message
    });
  }
}
```

```typescript
// src/routes/auth.routes.ts
import express from 'express';
import { JwtService } from '../auth/jwt.service';
import { UserService } from '../services/user.service';

const router = express.Router();
const jwtService = new JwtService();
const userService = new UserService();

// 登录
router.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // 验证用户
    const user = await userService.validateCredentials(email, password);
    if (!user) {
      return res.status(401).json({
        error: 'invalid_credentials',
        message: 'Invalid email or password'
      });
    }
    
    // 生成 token
    const token = jwtService.sign({
      userId: user.id,
      email: user.email
    });
    
    res.json({
      success: true,
      data: {
        token,
        user: {
          id: user.id,
          email: user.email,
          name: user.name
        }
      }
    });
  } catch (error) {
    res.status(500).json({
      error: 'server_error',
      message: 'An error occurred during login'
    });
  }
});

// 刷新 token
router.post('/refresh', authMiddleware, async (req, res) => {
  try {
    const oldToken = req.headers.authorization!.substring(7);
    const newToken = jwtService.refresh(oldToken);
    
    res.json({
      success: true,
      data: { token: newToken }
    });
  } catch (error: any) {
    res.status(401).json({
      error: 'invalid_token',
      message: error.message
    });
  }
});

export default router;
```

### 客户端使用

```typescript
// 登录
const loginResponse = await fetch('/api/auth/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ email, password })
});

const { data } = await loginResponse.json();
const token = data.token;

// 保存 token
localStorage.setItem('auth_token', token);

// 后续请求携带 token
const response = await fetch('/api/users/me', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

### JWT 最佳实践

**✅ 推荐**：
- 使用 HTTPS 传输
- 设置合理的过期时间（7-30天）
- 不存储敏感信息在 payload 中
- 使用强密钥（至少 256 位）
- 实现 token 刷新机制

**❌ 不推荐**：
- 在 URL 中传递 token
- 永不过期的 token
- 在 payload 中存储密码
- 使用弱密钥

---

## 2️⃣ API Key - 服务间调用

### 适用场景

- 第三方服务集成
- 服务间 API 调用
- 无需用户上下文的场景

### 实现方案

```typescript
// 生成 API Key
import crypto from 'crypto';

function generateApiKey(): string {
  return crypto.randomBytes(32).toString('hex');
}

// 存储格式
interface ApiKey {
  id: string;
  key: string;  // 哈希后的值
  name: string;
  userId: string;
  permissions: string[];
  createdAt: Date;
  lastUsedAt?: Date;
  expiresAt?: Date;
}
```

```typescript
// 验证 API Key
export function apiKeyMiddleware(req: Request, res: Response, next: NextFunction) {
  try {
    const apiKey = req.headers['x-api-key'] as string;
    
    if (!apiKey) {
      return res.status(401).json({
        error: 'missing_api_key',
        message: 'API Key is required'
      });
    }
    
    // 验证 API Key
    const keyRecord = await apiKeyService.validate(apiKey);
    if (!keyRecord) {
      return res.status(401).json({
        error: 'invalid_api_key',
        message: 'Invalid or expired API Key'
      });
    }
    
    // 检查权限
    if (!keyRecord.permissions.includes(req.path)) {
      return res.status(403).json({
        error: 'insufficient_permissions',
        message: 'API Key does not have required permissions'
      });
    }
    
    // 更新最后使用时间
    await apiKeyService.updateLastUsed(keyRecord.id);
    
    req.apiKey = keyRecord;
    next();
  } catch (error) {
    res.status(500).json({
      error: 'server_error',
      message: 'Error validating API Key'
    });
  }
}
```

### 客户端使用

```typescript
fetch('/api/sync/push', {
  method: 'POST',
  headers: {
    'X-API-Key': 'sk_live_abc123...',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ /* data */ })
});
```

---

## 3️⃣ OAuth 2.0 - 第三方登录

### 常见流程

```
┌──────────────────────────────────────────────────────────────────┐
│                   OAuth 2.0 Authorization Code Flow              │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   1. 用户点击"使用 Google 登录"                                    │
│   ┌─────────┐                                   ┌──────────┐    │
│   │ Client  │  重定向到 Google 授权页面           │  Google  │    │
│   │         │ ──────────────────────────────►   │  OAuth   │    │
│   └─────────┘                                   └──────────┘    │
│                                                       │          │
│   2. 用户授权                                          │          │
│                                                       ↓          │
│   ┌─────────┐                                   ┌──────────┐    │
│   │         │  重定向回调 + authorization_code    │          │    │
│   │         │ ◄──────────────────────────────   │          │    │
│   └─────────┘                                   └──────────┘    │
│        │                                                         │
│        │ 3. 交换 code 获取 access_token                          │
│        ↓                                                         │
│   ┌─────────┐                                   ┌──────────┐    │
│   │  Your   │  POST /token { code }              │  Google  │    │
│   │ Server  │ ──────────────────────────────►   │  OAuth   │    │
│   │         │                                   │          │    │
│   │         │  { access_token, refresh_token }   │          │    │
│   │         │ ◄──────────────────────────────   │          │    │
│   └─────────┘                                   └──────────┘    │
│        │                                                         │
│        │ 4. 使用 access_token 获取用户信息                        │
│        ↓                                                         │
│   ┌─────────┐                                   ┌──────────┐    │
│   │         │  GET /userinfo                     │  Google  │    │
│   │         │  Authorization: Bearer token       │   API    │    │
│   │         │ ──────────────────────────────►   │          │    │
│   │         │                                   │          │    │
│   │         │  { email, name, picture }          │          │    │
│   │         │ ◄──────────────────────────────   │          │    │
│   └─────────┘                                   └──────────┘    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 使用 Passport.js 实现

```typescript
// src/auth/oauth.strategy.ts
import passport from 'passport';
import { Strategy as GoogleStrategy } from 'passport-google-oauth20';

passport.use(
  new GoogleStrategy(
    {
      clientID: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      callbackURL: '/api/auth/google/callback'
    },
    async (accessToken, refreshToken, profile, done) => {
      try {
        // 查找或创建用户
        let user = await userService.findByEmail(profile.emails[0].value);
        
        if (!user) {
          user = await userService.create({
            email: profile.emails[0].value,
            name: profile.displayName,
            avatar: profile.photos[0].value,
            provider: 'google',
            providerId: profile.id
          });
        }
        
        done(null, user);
      } catch (error) {
        done(error);
      }
    }
  )
);
```

```typescript
// src/routes/auth.routes.ts
// 发起 OAuth 授权
router.get('/auth/google', 
  passport.authenticate('google', {
    scope: ['profile', 'email']
  })
);

// OAuth 回调
router.get('/auth/google/callback',
  passport.authenticate('google', { session: false }),
  (req, res) => {
    // 生成 JWT
    const token = jwtService.sign({
      userId: req.user.id,
      email: req.user.email
    });
    
    // 重定向到前端，携带 token
    res.redirect(`http://localhost:3000/auth/callback?token=${token}`);
  }
);
```

---

## 🛡️ 安全最佳实践

### 1. HTTPS Only

```typescript
// 强制 HTTPS
app.use((req, res, next) => {
  if (req.header('x-forwarded-proto') !== 'https' && process.env.NODE_ENV === 'production') {
    res.redirect(`https://${req.header('host')}${req.url}`);
  } else {
    next();
  }
});
```

### 2. Rate Limiting

```typescript
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 100, // 最多 100 次请求
  message: {
    error: 'rate_limit_exceeded',
    message: 'Too many requests, please try again later'
  }
});

app.use('/api/', limiter);
```

### 3. CORS 配置

```typescript
import cors from 'cors';

app.use(cors({
  origin: process.env.NODE_ENV === 'production'
    ? ['https://dailyuse.app']
    : ['http://localhost:3000'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

### 4. 密码加密

```typescript
import bcrypt from 'bcrypt';

// 注册时加密密码
const hashedPassword = await bcrypt.hash(password, 10);

// 登录时验证密码
const isValid = await bcrypt.compare(password, user.hashedPassword);
```

### 5. 防止 XSS

```typescript
import helmet from 'helmet';

app.use(helmet());
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    scriptSrc: ["'self'"],
    imgSrc: ["'self'", 'data:', 'https:']
  }
}));
```

### 6. 输入验证

```typescript
import { body, validationResult } from 'express-validator';

router.post('/login',
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 8 }),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // 处理登录逻辑
  }
);
```

---

## 🔗 相关资源

- [[认证业务]] - 认证业务逻辑
- [[Cookie、Session、Token]] - 认证机制对比
- [[Token认证的实现流程]] - Token 认证实现
- [[RESTful-API]] - REST API 设计

---

## 💡 常见问题

### Q: JWT vs Session Cookie？

**JWT 优势**：
- ✅ 无状态，易于横向扩展
- ✅ 跨域友好
- ✅ 移动应用友好

**Session 优势**：
- ✅ 服务端可以主动撤销
- ✅ 不会暴露用户信息

**推荐**：单页应用/移动应用用 JWT，传统 Web 应用用 Session

### Q: 如何撤销 JWT？

JWT 本身无法撤销，解决方案：

1. **Token 黑名单**（Redis）
2. **短过期时间 + 刷新 Token**
3. **版本号机制**（用户改密码时版本号+1）

### Q: Refresh Token 如何实现？

```typescript
// 双 Token 机制
{
  "accessToken": "短期token(15分钟)",
  "refreshToken": "长期token(30天)"
}

// 当 accessToken 过期时
POST /api/auth/refresh
Authorization: Bearer <refreshToken>

// 返回新的 accessToken
{
  "accessToken": "new_token"
}
```
