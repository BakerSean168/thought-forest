---
tags:
  - tech/dev/api
  - type/concept
  - status/growing
description: OpenAPI_Swagger
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[后端开发 MOC]] | [[项目实践 MOC]]

---


# OpenAPI_Swagger 

## 基础概念
用于标准化和文档化接口
### **（1）OpenAPI 规范（OAS）**

- 一种 **YAML/JSON 格式的文件**，用于描述 API 的：
    - 端点（URL 路径）
    - 请求/响应格式（参数、数据类型）
    - 认证方式（如 API Key、OAuth2）
    - 错误码、示例等元数据
- 最新版本：**OpenAPI 3.1**（2021年发布）

### **（2）Swagger 工具集**

Swagger 是 OpenAPI 的**原始实现**，现已成为其生态系统的一部分，包含以下工具：

- **Swagger Editor**：在线编辑 OpenAPI 文件的工具。
- **Swagger UI**：将 OpenAPI 文件渲染为交互式文档（可视化 API 调用界面）。
- **Swagger Codegen**：根据 OpenAPI 文件生成客户端/服务端代码。

> [!NOTE] 类比
> OpenAPI 像「HTML 标准」，Swagger 像「Chrome 浏览器 + 开发者工具」。

## 📖 在 Node.js/Express 中使用

### 方案一：使用 swagger-jsdoc（代码注释）

#### 步骤 1：安装依赖

```bash
pnpm add swagger-jsdoc swagger-ui-express
pnpm add -D @types/swagger-jsdoc @types/swagger-ui-express
```

#### 步骤 2：创建 Swagger 配置文件

**swagger.ts**：
```typescript
import swaggerJsdoc from 'swagger-jsdoc';
import swaggerUi from 'swagger-ui-express';
import type { Express } from 'express';

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'DailyUse API',
      version: '1.0.0',
      description: 'DailyUse 应用的 REST API 文档',
      // ... 其他元数据
    },
    servers: [
      {
        url: 'http://localhost:3888/api/v1',
        description: '开发环境'
      }
    ],
    components: {
      // 安全方案
      securitySchemes: {
        bearerAuth: {
          type: 'http',
          scheme: 'bearer',
          bearerFormat: 'JWT'
        }
      },
      // 复用的数据结构
      schemas: {
        ApiResponse: { /* ... */ },
        ErrorResponse: { /* ... */ }
      }
    }
  },
  // 扫描哪些文件获取 API 注释
  apis: [
    './src/modules/**/routes.ts',
    './src/shared/types/*.ts'
  ]
};

const specs = swaggerJsdoc(options);

export function setupSwagger(app: Express): void {
  // Swagger UI 界面
  app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
  
  // OpenAPI JSON 接口
  app.get('/api-docs.json', (req, res) => {
    res.json(specs);
  });
}
```

#### 步骤 3：在 Express 应用中集成

**app.ts**：
```typescript
import { setupSwagger } from './config/swagger.js';

const app = express();

// ⚠️ 重要：Swagger 必须在路由和 404 处理器之前设置
setupSwagger(app);

// 其他路由
app.use('/api/v1', apiRoutes);

// 404 处理器
app.use((_req, res) => {
  res.status(404).json({ code: 'NOT_FOUND', message: 'Not Found' });
});
```

#### 步骤 4：添加路由注释

**在路由文件中添加 JSDoc 注释**：
```typescript
/**
 * @swagger
 * /auth/login:
 *   post:
 *     tags: [Authentication]
 *     summary: 用户登录
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required: [username, password]
 *             properties:
 *               username: { type: string, example: "Test1" }
 *               password: { type: string, example: "Llh123123" }
 *     responses:
 *       200:
 *         description: 登录成功
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/ApiResponse'
 */
router.post('/auth/login', loginController);
```


### 🔧 3. 常见问题和解决方案

#### 问题 1：404 Not Found
**原因**：Swagger 设置在 404 处理器之后
**解决**：将 `setupSwagger(app)` 放在所有路由之前

#### 问题 2：依赖未安装
**原因**：swagger-jsdoc 或 swagger-ui-express 未安装
**解决**：
```bash
pnpm add swagger-jsdoc swagger-ui-express @types/swagger-jsdoc @types/swagger-ui-express
```

#### 问题 3：路由扫描失败
**原因**：apis 路径配置错误
**解决**：检查 swagger 配置中的 `apis` 数组路径

#### 问题 4：TypeScript 模块解析错误
**原因**：ES 模块和 CommonJS 混用
**解决**：使用 `.js` 扩展名导入 TypeScript 编译后的文件

### 📝 4. 最佳实践

#### 1. 结构化文档
```typescript
// 使用 components 定义可复用的结构
components: {
  schemas: {
    User: { /* 用户模型 */ },
    ApiResponse: { /* 标准响应格式 */ }
  },
  securitySchemes: {
    bearerAuth: { /* JWT 认证 */ }
  }
}
```

#### 2. 标签分组
```typescript
/**
 * @swagger
 * tags:
 *   - name: Authentication
 *     description: 用户认证相关接口
 *   - name: Schedules
 *     description: 任务调度相关接口
 */
```

#### 3. 完整的错误响应
```typescript
responses: {
  400: {
    description: '请求参数错误',
    content: {
      'application/json': {
        schema: { $ref: '#/components/schemas/ErrorResponse' }
      }
    }
  }
}
```

### 🚀 5. 高级功能

#### 自动导出 OpenAPI 文档
```typescript
// src/scripts/export-api-docs.js
const specs = swaggerJsdoc(options);
fs.writeFileSync('docs/api-docs.json', JSON.stringify(specs, null, 2));
```

#### 多环境配置
```typescript
const servers = process.env.NODE_ENV === 'production' 
  ? [{ url: 'https://api.dailyuse.com/v1', description: '生产环境' }]
  : [{ url: 'http://localhost:3888/api/v1', description: '开发环境' }];
```

## 实战经验
## 经验总结
## 信息参考

