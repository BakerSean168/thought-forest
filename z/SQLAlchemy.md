---
tags:
  - tech/lang/python
  - type/concept
  - status/growing
description: SQLAlchemy
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Python MOC]] | [[后端开发 MOC]]

---


# SQLAlchemy

## 基础概念

组成层次：
1. Engine：数据库连接与方言（dialect）管理
2. Connection / Session：事务性操作上下文
3. SQL Expression (Core)：可组合的 SQL 语法树
4. ORM：将类映射到表（Declarative）
5. Mapper：类与表之间映射配置

Core vs ORM：Core 偏底层显式构造 SQL；ORM 聚焦对象持久化与关系导航。

2.0 风格：去掉隐式自动提交 / 过时 API；推荐使用 `with Session.begin()`、`select()` 新式查询。

常见概念：
- 表达式构造：`select() / insert() / update() / delete()`
- 关系：`relationship()` + `ForeignKey` + `back_populates`
- 延迟加载策略：lazy / selectin / joined / subquery
- 会话生命周期：Session 代表一个工作单元（Unit of Work）
- 事务：Session.begin / commit / rollback
- Async 支持：`sqlalchemy.ext.asyncio` + async engine + async session

## 使用指南

### 安装
```bash
pip install sqlalchemy psycopg2-binary
pip install "sqlalchemy[asyncio]" asyncpg  # 异步 Postgres
```

### 定义模型（2.0 Declarative）
```python
from typing import List
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship
from sqlalchemy import String, Integer, ForeignKey

class Base(DeclarativeBase):
  pass

class User(Base):
  __tablename__ = 'users'
  id: Mapped[int] = mapped_column(primary_key=True)
  name: Mapped[str] = mapped_column(String(50), index=True)
  articles: Mapped[List['Article']] = relationship(back_populates="author")

class Article(Base):
  __tablename__ = 'articles'
  id: Mapped[int] = mapped_column(primary_key=True)
  title: Mapped[str]
  author_id: Mapped[int] = mapped_column(ForeignKey('users.id'))
  author: Mapped['User'] = relationship(back_populates="articles")
```

### 创建引擎与建表
```python
from sqlalchemy import create_engine
engine = create_engine("sqlite:///./app.db", echo=False, future=True)
Base.metadata.create_all(engine)
```

### Session 使用
```python
from sqlalchemy.orm import Session
with Session(engine) as session:
  u = User(name='alice')
  session.add(u)
  session.commit()
```

### 查询（2.0 select）
```python
from sqlalchemy import select
with Session(engine) as session:
  stmt = select(User).where(User.name == 'alice')
  user = session.scalars(stmt).first()
```

### 关系加载策略
```python
stmt = select(User).options(selectinload(User.articles))
```

### 异步示例
```python
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker
from sqlalchemy import select

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/db", echo=False)
AsyncSession = async_sessionmaker(engine, expire_on_commit=False)

async def get_user(name: str):
  async with AsyncSession() as session:
    stmt = select(User).where(User.name == name)
    return await session.scalars(stmt)
```

### 乐观锁
添加 `version_id_col` + `version_id_generator` 支持并发控制。

### Schema 演进
使用 Alembic：版本迁移（env.py + versions/ 生成脚本）。

### 常用模式
- Repository 抽象：封装常见 CRUD
- Unit of Work：统一提交/回滚（事务边界）
- DTO 分离：避免 ORM 模型直接暴露到 API

## 实战经验

1. 避免 n+1 查询：使用 `selectinload / joinedload`
2. 控制会话生命周期：每请求一个 Session（Web 框架中依赖注入）
3. 提前规划索引：高频过滤列 + 组合索引
4. 大批量插入：`session.bulk_save_objects` 或 Core `engine.execute(insert())`
5. 延迟提交：聚合多个变更后一次 commit 减少 flush 频率
6. 读写分离：自定义路由 Engine（读从写主）
7. 连接池调优：`pool_size / max_overflow / pool_recycle`
8. 超时控制：`pool_timeout` 报警监控
9. 防止对象膨胀：关闭 `expire_on_commit` 时确保缓存一致性策略
10. 异步性能：I/O 密集时使用 async 版本 + asyncpg

常见坑：
- 忘记 Session 关闭导致连接泄漏
- 直接在模型属性里写复杂逻辑（可用 hybrid_property）
- 关系默认 lazy 导致隐式多次查询
- 大事务长时间占锁，需拆分
- ORM 模型与 Pydantic 模型耦合

## 经验总结

核心经验：清晰划分数据访问层，显式声明查询；对性能敏感部位回退 Core 手写 SQL；避免过度魔法。

注意：业务高并发下结合缓存 / CQRS / 读写分离 与 分页优化（覆盖索引）。

迁移规范：版本命名 + 严格 Review DDL 变更（锁表风险）。

## 信息参考

- 官方文档: <https://docs.sqlalchemy.org>
- 2.0 迁移指南: <https://docs.sqlalchemy.org/en/20/changelog/migration_20.html>
- ORM 加载策略: <https://docs.sqlalchemy.org/en/20/orm/loading_relationships.html>
- Async 文档: <https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html>
- Alembic: <https://alembic.sqlalchemy.org>

