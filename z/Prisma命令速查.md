---
tags:
  - tech/ops/database
  - type/howto
  - status/growing
description: Prisma命令速查
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[数据库 MOC]] | [[后端开发 MOC]]

---


# Prisma命令速查

## 命令速查

| 命令                                                                  | 说明                      |
| ------------------------------------------------------------------- | ----------------------- |
| `pnpm exec prisma generate`                                         | 生成 Prisma Client        |
| `pnpm exec prisma validate`                                         | 验证 schema 有效性           |
| `pnpm exec prisma format`                                           | 格式化 schema 文件           |
| `pnpm exec prisma migrate dev`                                      | 创建并应用迁移                 |
| `pnpm exec prisma db push`                                          | 同步 schema 到数据库          |
| `pnpm exec prisma studio`                                           | 打开 Prisma Studio Web UI |
|  pnpm prisma migrate dev --name init;                               |                         |
| `docker exec dailyuse-prod-api pnpm prisma db push --skip-generate` | 在docker部署后执行，创建数据表      |

清除 Prisma 缓存的方法：  
  
方法 1: 删除生成的 Prisma Client (推荐)  
rm -rf ../../node_modules/.prisma  
rm -rf ../../node_modules/@prisma/client  
pnpm prisma generate  
  
方法 2: 使用 Prisma 内置清理命令  
pnpm prisma generate --no-engine  
pnpm prisma generate  
  
方法 3: 重启 Node.js 进程 (最简单)  
- 停止 API 服务 (Ctrl+C)  
- 重新启动 API 服务  
- Node.js 会重新加载模块  
  
方法 4: 使用环境变量强制重新生成  
PRISMA_SKIP_POSTINSTALL_GENERATE=false pnpm prisma generate
