---
tags:
  - tech/ops/database
  - type/concept
  - status/growing
description: Redis 内存数据库基础与应用
created: 2025-09-28T16:30:45
updated: 2025-12-07T17:00:00
---

# Redis

**上级索引**：[[000_Home]]

## 基础概念

定位：内存优先（Memory-first）键值存储，使用单线程事件循环（6.0+ 引入 I/O 多线程）。

核心数据结构：

1. String（底层 SDS 动态字符串）
2. List（QuickList）
3. Hash（ziplist/hashtable 自适应 -> listpack）
4. Set（intset / hashtable）
5. Sorted Set（跳表 + dict）
6. Bitmap / Bitfield / HyperLogLog / GEO / Stream

过期键处理：惰性删除 + 定期采样删除（近似 LRU）。

持久化：

- RDB（快照）：fork 子进程保存紧凑二进制快照
- AOF（追加日志）：写命令日志，支持 rewrite 压缩
- 混合持久化：RDB header + AOF 增量

内存淘汰策略：`noeviction / allkeys-lru / volatile-lru / allkeys-random / volatile-ttl / volatile-lfu / allkeys-lfu`。

高可用：

- 主从复制（PSYNC）
- Sentinel：自动故障转移
- Cluster：16384 槽（hash slot）分片 + gossip + 重定向（MOVED/ASK）

## 使用指南

### 基本操作

```redis
SET user:1 '{"id":1,"name":"Alice"}' EX 60
GET user:1
DEL user:1
INCR counter:pv
```

### Hash 适用场景

```redis
HSET user:1 name Alice age 18
HGETALL user:1
```
适合字段较少（<100）或增量更新；避免大 Hash 导致重哈希阻塞。

### 分布式锁（简易）

```redis
SET lock:order 12345 NX EX 10
```
续期需 Lua 脚本验证标识。
推荐 RedLock（对多个 master）但需了解 CAP 权衡。

### Pub/Sub 与 Stream

- Pub/Sub：实时推送，消息不可回溯
- Stream：保留历史（消费者组 offset）适合日志、事件溯源

### LUA 脚本原子性

通过 `EVAL` 保证脚本内操作原子；注意脚本长度与执行时间避免阻塞。

### 典型缓存模式

- Cache Aside（旁路缓存）：读 miss -> DB -> set；写 -> DB + del key
- Read Through / Write Through（由中间层代理）
- Write Back（异步刷新，风险高）

### 防击穿/雪崩/穿透

- 穿透：缓存空值；Bloom Filter 预过滤
- 击穿（热点过期）：互斥锁 + 逻辑过期 + 预热
- 雪崩：随机过期时间 + 多级缓存 + 限流熔断

## 实战经验

1. Key 设计：`业务:实体:标识[:字段]`，长度控制在 50 字符内；避免使用空格
2. 控制大 Key：单个值 > 1MB 考虑拆分；List/Set/ZSet 元素数量监控
3. Pipeline：批量减少 RTT；注意命令堆积导致内存暴涨
4. 避免使用 KEYS * 在生产；改用 SCAN 游标迭代
5. 统计去重：用 HyperLogLog；计数用 INCR 或者 bitmap（日期打点）
6. 频率限制：Lua + INCR + TTL 或者 Redis-Cell（令牌桶）
7. 延迟任务：ZSet score = timestamp；定期轮询 <= now 元素
8. 分布式锁谨慎：确保 设置 + 过期 原子，释放校验锁值
9. 内存水位：配置 `maxmemory` + 合理淘汰策略 + 监控 hits/misses
10. AOF 重写与 RDB fork 会短暂阻塞，业务高峰避免触发

常见坑：
- 大量过期同时失效（雪崩）
- Hash Tag 使用不当导致 Cluster 二次请求
- 主从延迟下读取从节点导致“读旧”
- 不合理使用事务（MULTI/EXEC）认为能回滚（Redis 事务不支持回滚语义）

## 经验总结

定位清晰：缓存 + 短期会话 + 排行榜 + 计数 + 分布式协调（锁/限流）。不适合：强事务、大规模复杂查询、长久冷数据。

优化思路：
- 减少网络往返：pipeline / client-side caching
- 控制内存碎片：合理 value 大小 + 定期观察 info memory
- 熔断 / 限流保护下游
- 多级缓存（本地 LRU -> Redis -> DB）

关键指标：命中率 / 内存使用 / evicted_keys / replication lag / latency percentile。

## 信息参考

- 官网: <https://redis.io>
- 命令: <https://redis.io/commands>
- 设计文档: <https://redis.io/docs>
- RedLock 讨论: <https://redis.io/docs/interact/programmability/distlock/>
- 性能调优: <https://redis.io/docs/management/optimization/>
