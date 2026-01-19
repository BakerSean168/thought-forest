---
tags:
  - tech/lang/nodejs
  - type/howto
  - status/evergreen
description: 使用prisma重置数据库
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Node.js MOC]] | [[后端框架 MOC]]

---


要重置数据库并使用新的Prisma Model结构，需彻底清理旧数据和迁移记录，重新初始化。以下是**安全且完整的步骤**（适用于开发环境，生产环境需谨慎）：


### 一、备份旧数据（可选，避免丢失重要信息）
如果旧数据还有用，先导出备份（以PostgreSQL为例）：
```bash
# 导出整个数据库（替换为你的配置）
pg_dump -h localhost -p 5432 -U 用户名 -d 数据库名 > 旧数据备份.sql
```


### 二、清理旧的迁移记录和数据库
#### 1. 删除本地迁移文件
删除`prisma/migrations`文件夹（这是Prisma的迁移历史记录）：
```bash
# 进入项目的prisma目录
cd apps/api/prisma
rm -rf migrations  # 或手动删除文件夹
```


#### 2. 清空数据库

方式1：
```sql
pnpm prisma migrate reset --force;
```



### 三、用新Model重新初始化数据库
#### 1. 生成新的迁移文件（初始化）
在项目目录（`apps/api`）执行：
```bash
pnpm prisma migrate dev --name init
```
- 这会基于当前的Prisma Model生成**初始迁移文件**，并自动应用到数据库（创建新表）。
- 执行成功后，`prisma/migrations`会生成新的迁移记录。


#### 2. 验证新结构
用Prisma Studio查看新表结构是否正确：
```bash
pnpm prisma studio
```
打开浏览器访问`http://localhost:5555`，即可看到新的数据库表。


### 四、补充：若需填充初始数据
如果需要向新表中插入初始数据（如测试数据），可以：
1. 编写种子脚本（`prisma/seed.ts`）：
```typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

async function main() {
  // 插入初始数据（示例）
  await prisma.accounts.create({
    data: {
      email: 'test@example.com',
      name: '测试用户'
    }
  });
}

main()
  .catch(e => console.error(e))
  .finally(async () => await prisma.$disconnect());
```

2. 在`package.json`中添加种子命令：
```json
{
  "scripts": {
    "prisma:seed": "ts-node prisma/seed.ts"
  }
}
```

3. 执行种子脚本：
```bash
pnpm prisma:seed
```


### 关键注意事项
- **仅用于开发环境**：生产环境不要直接删库，需通过迁移脚本逐步升级（或备份后迁移）。
- **确保Prisma Model正确**：执行`migrate dev`前，先检查Model语法（可执行`pnpm prisma validate`验证）。
- **清理缓存**：若执行后仍有旧表，可能是Prisma缓存问题，执行`pnpm prisma generate`重新生成客户端。


要不要我帮你整理一份**Prisma数据库重置的一键操作脚本**，方便你后续快速执行？
