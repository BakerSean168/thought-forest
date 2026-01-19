---
tags:
  - tech/ops/database
  - type/concept
  - status/growing
description: SQLite
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[数据库 MOC]] | [[后端开发 MOC]]

---


# SQLite

## 基础概念

特点：
1. 服务器less：应用直接链接库文件（*.db）
2. 单文件（+ WAL 辅助文件）跨平台复制方便
3. 事务：完整 ACID，基于日志 / WAL
4. 锁机制：数据库级/页级（WAL 模式提升并发读）
5. 动态类型（manifest typing）——列声明类型但运行时可存不同类型（不推荐滥用）
6. 可扩展（虚拟表 / 自定义函数 / 自定义聚合）
7. 版本语义：向前兼容性高

适用：本地持久层、嵌入式设备、测试原型、Edge 缓存、轻量分析。 不适用：高并发写、复杂分布式事务、大规模 OLTP。

文件结构：B-Tree 页面（默认 4KB）+ freelist + schema table (sqlite_master)。

WAL 模式：写入追加到 wal 文件，checkpoint 时合并回主文件；优点：读写并发提升；代价：需要定期 checkpoint 和磁盘空间管理。

## 使用指南

### 启动 / CLI
```bash
sqlite3 app.db
```

### 建表
```sql
CREATE TABLE users (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	name TEXT NOT NULL,
	email TEXT UNIQUE,
	created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 索引
```sql
CREATE INDEX idx_users_email ON users(email);
```

### 事务
```sql
BEGIN;
INSERT INTO users(name,email) VALUES('a','a@ex.com');
COMMIT;
```

### 参数化（Python）
```python
import sqlite3
conn = sqlite3.connect('app.db')
cur = conn.cursor()
cur.execute('SELECT * FROM users WHERE email = ?', ('a@ex.com',))
rows = cur.fetchall()
```

### WAL 模式开启
```sql
PRAGMA journal_mode=WAL;
PRAGMA synchronous=NORMAL; -- 或 FULL/EXTRA
```

### Vacuum / Analyze
```sql
VACUUM;      -- 重建文件减少碎片
ANALYZE;     -- 统计信息更新，优化查询计划
```

### JSON 支持
```sql
SELECT json_extract('{"a":1,"b":2}', '$.b');
```

### 虚拟表 (FTS5) 示例
```sql
CREATE VIRTUAL TABLE docs USING fts5(title, body);
INSERT INTO docs VALUES('hello','sqlite fts test');
SELECT * FROM docs WHERE docs MATCH 'hello';
```

## 实战经验

1. 并发写限制：适当开启 WAL + 限制写事务时间
2. 控制长事务：避免锁表导致别的连接阻塞
3. 连接复用：同进程内保持单例连接池（或短期使用上下文管理）
4. 索引选择：高选择性列 + 组合查询前缀优化
5. 避免 SELECT *：减少 I/O + 更稳定 schema 演进
6. 数据迁移：手工 `ALTER TABLE` 受限 -> 采用新表复制 + 重命名
7. 防止文件损坏：启用 WAL + 可靠存储（避免网络共享不一致）
8. 备份：在线备份 API 或 `VACUUM INTO` 生成快照
9. 加密：使用 SQLCipher（独立编译）
10. Monitor：`PRAGMA integrity_check;` / `EXPLAIN QUERY PLAN` 分析

常见坑：
- NFS/共享盘不安全（锁实现依赖本地文件系统）
- 未正确关闭连接导致临时文件残留
- 频繁 VACUUM 反伤性能（按需）
- 字段类型约束不严格（需应用层校验）

## 经验总结

设计姿势：把 SQLite 当只读缓存或离线同步中间层（定期与主库同步）；通过分表/分文件隔离热点写。

优化路径：分析 `EXPLAIN QUERY PLAN` -> 添加索引 -> 控制事务粒度 -> 适度 WAL 调参（checkpoint, synchronous）。

迁移策略：创建新表 + 数据复制 + 建索引 + 替换；避免频繁复杂 ALTER。

## 信息参考

- 官方: <https://sqlite.org>
- 文档: <https://sqlite.org/docs.html>
- FTS5: <https://sqlite.org/fts5.html>
- WAL: <https://sqlite.org/wal.html>
- SQLCipher: <https://www.zetetic.net/sqlcipher/>
