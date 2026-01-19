---
tags:
  - tech/lang/javascript
  - tech/ops/backend
  - type/howto
  - status/growing
description: Bull - Redis 驱动的 Node.js 任务队列库
created: 2025-08-10T09:15:30
updated: 2025-12-07T17:00:00
---

> [!info] **上级索引**
> [[JavaScript 工具库 MOC]] | [[Node.js MOC]]

---

# Bull 任务队列库

Bull 是一个基于 Redis 的 Node.js 高性能任务队列库，用于处理后台任务、分布式作业和消息队列。

## 基础概念

### 核心特点

- **基于 Redis**：利用 Redis 实现高性能、持久化的任务队列
- **分布式处理**：支持多进程/多服务器并行处理任务
- **任务调度**：支持延迟任务、重复任务、优先级任务
- **可靠性**：内置重试、失败处理、超时机制
- **监控友好**：提供丰富的事件和 Dashboard 工具

### 应用场景

| 场景 | 说明 |
|------|------|
| 邮件发送 | 批量邮件、延迟发送 |
| 文件处理 | 图片压缩、视频转码 |
| 数据同步 | 数据库同步、缓存更新 |
| 定时任务 | Cron 式定时执行 |
| 第三方 API | 限速调用、重试机制 |

## 使用指南

### 安装

```bash
npm install bull
npm install @types/bull -D  # TypeScript 支持
```

确保有可用的 Redis 实例：
```bash
docker run -d --name redis -p 6379:6379 redis:alpine
```

### 基础用法

#### 1. 创建队列

```typescript
import Bull from 'bull';

// 创建队列
const emailQueue = new Bull('email-queue', {
  redis: {
    host: 'localhost',
    port: 6379,
  },
});

// 添加任务
await emailQueue.add({
  to: 'user@example.com',
  subject: 'Welcome!',
  body: 'Hello, welcome to our service.',
});
```

#### 2. 处理任务

```typescript
// 定义任务处理器
emailQueue.process(async (job) => {
  console.log(`Processing job ${job.id}`);
  console.log('Data:', job.data);
  
  await sendEmail(job.data);
  
  return { sent: true };
});
```

#### 3. 延迟任务

```typescript
// 5分钟后执行
await queue.add(data, { delay: 5 * 60 * 1000 });
```

#### 4. 重复任务（Cron）

```typescript
// 每天早上9点执行
await queue.add(data, {
  repeat: {
    cron: '0 9 * * *',
  },
});
```

#### 5. 优先级任务

```typescript
// 优先级：数字越小优先级越高
await queue.add(highPriorityData, { priority: 1 });
await queue.add(normalData, { priority: 5 });
await queue.add(lowPriorityData, { priority: 10 });
```

### 事件监听

```typescript
queue.on('completed', (job, result) => {
  console.log(`Job ${job.id} completed with result:`, result);
});

queue.on('failed', (job, error) => {
  console.log(`Job ${job.id} failed:`, error.message);
});

queue.on('progress', (job, progress) => {
  console.log(`Job ${job.id} is ${progress}% complete`);
});
```

### 任务进度报告

```typescript
queue.process(async (job) => {
  for (let i = 0; i <= 100; i += 10) {
    await someWork();
    job.progress(i);  // 报告进度
  }
  return 'Done';
});
```

## 高级配置

### 重试策略

```typescript
await queue.add(data, {
  attempts: 3,           // 最多重试3次
  backoff: {
    type: 'exponential', // 指数退避
    delay: 1000,         // 初始延迟1秒
  },
});
```

### 并发控制

```typescript
// 同时处理最多5个任务
queue.process(5, async (job) => {
  // ...
});
```

### 任务超时

```typescript
await queue.add(data, {
  timeout: 30000,  // 30秒超时
});
```

## 实战经验

### 最佳实践

1. **幂等设计**：任务处理器应设计为幂等，因为可能重复执行
2. **错误处理**：始终捕获异常，让任务正确失败而非崩溃
3. **监控告警**：使用 Bull Dashboard 或 Arena 监控队列健康
4. **清理策略**：定期清理已完成任务，避免 Redis 内存膨胀

### 监控工具

```bash
# Bull Board（推荐）
npm install @bull-board/express @bull-board/api

# Arena
npm install bull-arena
```

## 参考资料

- **官方文档**：[Optimalbits/bull](https://github.com/Optimalbits/bull)
- **Bull Board**：[felixmosh/bull-board](https://github.com/felixmosh/bull-board)
- **BullMQ**：[Bull 的下一代版本](https://docs.bullmq.io/)

> [!tip] 提示
> Bull 已有继任者 **BullMQ**，新项目可考虑直接使用 BullMQ，它提供更好的 TypeScript 支持和更多功能。
