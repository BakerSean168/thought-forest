---
tags:
  - tech/lang/typescript
  - type/concept
  - status/growing
description: prisma
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[数据库 MOC]] | [[Node.js MOC]]

---


# Prisma 完整指南

## 📚 基础概念

### 什么是 Prisma？

Prisma 是一个现代化的数据库工具包（Node.js/TypeScript ORM 工具），包含以下三个主要工具：

1. **Prisma Client** - 类型安全的数据库客户端
2. **Prisma Migrate** - 数据库迁移工具
3. **Prisma Studio** - 现代化的数据库 GUI

### 核心文件

- **`schema.prisma`** - 数据库模式定义文件
- **`prisma/migrations/`** - 数据库迁移文件
- **`node_modules/.prisma/client/`** - 生成的客户端代码

### 数据建模

```prisma
// 数据源配置
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// 生成器配置
generator client {
  provider = "prisma-client-js"
}

// 模型定义
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())

  @@map("users")
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id])

  @@map("posts")
}
```

## 🚀 使用指南

[[prisma初体验（官方demo）]]

### 安装和初始化

```bash
# 安装 Prisma CLI
npm install prisma --save-dev
npm install @prisma/client

# 初始化 Prisma
npx prisma init

# 生成客户端
npx prisma generate
```

### 数据库连接

```typescript
// 1. 导入 Prisma Client
import { PrismaClient } from '@prisma/client'

// 2. 创建实例
const prisma = new PrismaClient()

// 3. 使用（推荐：全局单例模式）
export const prisma = new PrismaClient({
  log: ['query', 'info', 'warn', 'error'],
})
```

### 基础 CRUD 操作

```typescript
// 创建
const user = await prisma.user.create({
  data: {
    email: 'alice@example.com',
    name: 'Alice',
  },
})

// 查询
const users = await prisma.user.findMany({
  where: {
    email: {
      contains: '@example.com',
    },
  },
  include: {
    posts: true,
  },
})

// 更新
const updatedUser = await prisma.user.update({
  where: { id: 1 },
  data: { name: 'Alice Smith' },
})

// 删除
await prisma.user.delete({
  where: { id: 1 },
})
```

### 高级查询

```typescript
// 条件查询
const users = await prisma.user.findMany({
  where: {
    AND: [
      { name: { startsWith: 'A' } },
      { posts: { some: { published: true } } },
    ],
  },
})

// 分页
const users = await prisma.user.findMany({
  skip: 10,
  take: 10,
  orderBy: { createdAt: 'desc' },
})

// 聚合查询
const userCount = await prisma.user.count()
const postStats = await prisma.post.aggregate({
  _count: { id: true },
  _avg: { id: true },
  where: { published: true },
})
```

### 关系查询

```typescript
// 一对多关系
const userWithPosts = await prisma.user.findUnique({
  where: { id: 1 },
  include: {
    posts: {
      where: { published: true },
      orderBy: { createdAt: 'desc' },
    },
  },
})

// 多对多关系
const postWithCategories = await prisma.post.findUnique({
  where: { id: 1 },
  include: {
    categories: true,
  },
})

// 嵌套写入
const userWithPosts = await prisma.user.create({
  data: {
    email: 'alice@example.com',
    posts: {
      create: [
        { title: 'My first post' },
        { title: 'My second post' },
      ],
    },
  },
  include: {
    posts: true,
  },
})
```

### 事务处理

```typescript
// 基础事务
await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({
    data: { email: 'alice@example.com' },
  })

  const post = await tx.post.create({
    data: {
      title: 'My post',
      authorId: user.id,
    },
  })

  return { user, post }
})

// 交互式事务
await prisma.$transaction(async (tx) => {
  // 复杂的业务逻辑
  const result = await someComplexOperation(tx)
  return result
})
```

## 🛠️ 常用命令表格

| 命令                      | 描述               | 常用选项                                  |
| ----------------------- | ---------------- | ------------------------------------- |
| `prisma init`           | 初始化 Prisma 项目    | `--datasource-provider`               |
| `prisma generate`       | 生成 Prisma Client | `--schema`                            |
| `prisma db push`        | 推送 schema 到数据库   | `--force-reset`, `--accept-data-loss` |
| `prisma migrate dev`    | 开发环境迁移           | `--name`, `--create-only`             |
| `prisma migrate deploy` | 生产环境应用迁移         | -                                     |
| `prisma migrate reset`  | 重置数据库并重新应用迁移     | `--force`                             |
| `prisma migrate status` | 查看迁移状态           | -                                     |
| `prisma studio`         | 启动 Prisma Studio | `--port`, `--browser`                 |
| `prisma db seed`        | 运行种子数据           | -                                     |
| `prisma db pull`        | 从数据库拉取 schema    | `--force`                             |
| `prisma format`         | 格式化 schema 文件    | -                                     |
| `prisma validate`       | 验证 schema 文件     | -                                     |
| `prisma version`        | 查看版本信息           | -                                     |

## 🏆 实战经验

### 最佳实践

#### 1. 客户端实例管理

```typescript
// ❌ 错误：每次都创建新实例
export async function getUsers() {
  const prisma = new PrismaClient()
  return await prisma.user.findMany()
}

// ✅ 正确：使用全局单例
const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma
```

#### 2. 错误处理

```typescript
try {
  const user = await prisma.user.create({
    data: { email: 'existing@example.com' },
  })
} catch (error) {
  if (error instanceof Prisma.PrismaClientKnownRequestError) {
    if (error.code === 'P2002') {
      console.log('邮箱已存在')
    }
  }
  throw error
}
```

#### 3. 类型安全

```typescript
// 使用生成的类型
type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true }
}>

// 自定义查询类型
const userQuery = prisma.user.findUnique({
  where: { id: 1 },
  include: { posts: true },
}).then(user => user as UserWithPosts)
```

#### 4. 性能优化

```typescript
// 选择性查询字段
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    // 不选择 password 等敏感字段
  },
})

// 使用索引
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique  // 自动创建索引
  name      String?
  createdAt DateTime @default(now())

  @@index([createdAt])  // 手动创建索引
  @@map("users")
}
```

### 常见问题解决

#### 连接问题

```bash
# 检查数据库连接
npx prisma db push --preview-feature

# 查看连接字符串
echo $DATABASE_URL
```

#### 迁移冲突

```bash
# 查看迁移状态
npx prisma migrate status

# 解决冲突
npx prisma migrate resolve --applied 20240101120000_migration_name

# 强制重置（开发环境）
npx prisma migrate reset --force
```

#### 类型生成问题

```bash
# 清理并重新生成
rm -rf node_modules/.prisma
npx prisma generate

# 检查 schema 语法
npx prisma validate
```

## 📝 经验总结

## 数据库重置

[[使用prisma重置数据库]]

### 开发环境配置

```typescript
// .env
DATABASE_URL="postgresql://user:password@localhost:5432/dbname?schema=public"

// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
  previewFeatures = ["postgresqlExtensions"]
}
```

### 生产环境部署

```typescript
// 生产环境配置
const prisma = new PrismaClient({
  log: process.env.NODE_ENV === 'development' ? ['query', 'error', 'warn'] : ['error'],
  errorFormat: 'minimal',
})
```

### 数据库设计原则

1. **使用合适的数据类型**
   - `String` 用于文本
   - `Int` 用于整数
   - `Float` 用于小数
   - `Boolean` 用于布尔值
   - `DateTime` 用于时间戳
   - `Json` 用于复杂对象

2. **合理使用索引**
   - 外键字段自动创建索引
   - 常用查询字段添加索引
   - 避免过度索引

3. **规范化 vs 反规范化**
   - 优先考虑规范化设计
   - 性能关键场景可适当反规范化
   - 使用视图或计算字段

### 迁移策略

```bash
# 开发环境：快速迭代
npx prisma migrate dev --name "add-user-profile"

# 生产环境：安全部署
npx prisma migrate deploy

# 数据迁移：使用脚本
npx prisma db execute --file ./migrations/data-migration.sql
```

[[Prisma命令速查]]

## 📖 信息参考

### 官方资源

- [Prisma 官方文档](https://www.prisma.io/docs)
- [Prisma GitHub](https://github.com/prisma/prisma)
- [Prisma Data Platform](https://cloud.prisma.io)

### 学习资源

- [Prisma 入门教程](https://www.prisma.io/docs/getting-started)
- [Prisma 示例项目](https://github.com/prisma/prisma-examples)
- [Prisma 博客](https://www.prisma.io/blog)

### 社区资源

- [Prisma Discord](https://discord.gg/prisma)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/prisma)
- [Reddit r/Prisma](https://www.reddit.com/r/Prisma/)

### 相关工具

- **Prisma Studio** - 数据库 GUI
- **Prisma Client Extensions** - 客户端扩展
- **Prisma Accelerate** - 查询加速
- **Prisma Pulse** - 实时数据库订阅

### 版本兼容性

| Prisma 版本 | Node.js | 数据库支持 |
|-------------|---------|------------|
| 5.x | 16+ | PostgreSQL, MySQL, SQLite, SQL Server, MongoDB |
| 4.x | 14+ | PostgreSQL, MySQL, SQLite, SQL Server, MongoDB |
| 3.x | 12+ | PostgreSQL, MySQL, SQLite |

### 故障排除

```bash
# 查看详细日志
DEBUG="prisma:*" npx prisma db push

# 检查数据库连接
npx prisma db execute --file <(echo "SELECT 1")

# 重置客户端缓存
rm -rf node_modules/.prisma && npm install
```


