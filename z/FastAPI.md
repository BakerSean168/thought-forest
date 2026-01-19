---
tags:
  - tech/dev/python
  - type/concept
  - status/growing
description: FastAPI 高性能 Web API 框架
created: 2025-09-28T16:16:00
updated: 2025-12-07T17:00:00
---

# FastAPI

**上级索引**：[[000_Home]]

## 基础概念

技术栈组成：

- Starlette：ASGI Web 框架（路由 / 中间件 / WebSocket / 背景任务）
- Pydantic：数据验证与模型（请求体解析 / 响应序列化）
- Uvicorn / Hypercorn：ASGI Server

特点：

1. 基于 Python 类型注解自动生成 OpenAPI (Swagger / Redoc)
2. 依赖注入系统（函数参数声明式注入）
3. 异步优先（async/await）也兼容同步函数
4. 自动数据验证 + 文档 + JSON Schema
5. 高性能接近 Node / Go 级别（得益于 Starlette + uvloop 可选）
6. 支持 WebSocket、背景任务、事件处理（startup/shutdown）

适用场景：RESTful API、微服务后端、快速 MVP、需要严格数据模型的接口服务。

## 使用指南

### 最小示例

```python
from fastapi import FastAPI

app = FastAPI(title="Demo API")

@app.get("/ping")
async def ping():
  return {"msg": "pong"}
```

启动：

```bash
uvicorn main:app --reload --port 8000
```

### 请求体验证

```python
from pydantic import BaseModel, Field
from typing import Annotated
from fastapi import Body

class UserIn(BaseModel):
  username: str = Field(min_length=3, max_length=20)
  email: str
  age: int | None = Field(default=None, ge=0, le=120)

@app.post("/users")
async def create_user(payload: UserIn):
  return {"id": 1, **payload.model_dump()}
```

### 依赖注入 (Depends)

```python
from fastapi import Depends, Header, HTTPException

async def get_token(x_token: str | None = Header(None)):
  if x_token != "secret":
    raise HTTPException(401, detail="Invalid token")
  return x_token

@app.get("/secure")
async def secure_route(token=Depends(get_token)):
  return {"ok": True}
```

### 路径 / 查询参数

```python
from fastapi import Query

@app.get("/items/{item_id}")
async def get_item(item_id: int, q: str | None = Query(None, max_length=50)):
  return {"item_id": item_id, "q": q}
```

### 中间件

```python
from time import time
from fastapi import Request

@app.middleware("http")
async def add_timing(request: Request, call_next):
  start = time()
  resp = await call_next(request)
  resp.headers["X-Process-Time"] = f"{time()-start:.3f}s"
  return resp
```

### 事件钩子

```python
@app.on_event("startup")
async def init():
  # init db pool
  ...

@app.on_event("shutdown")
async def close():
  # close db
  ...
```

### 异步 vs 同步

同步函数在内部线程池执行（避免阻塞事件循环），耗时 IO 推荐 async 驱动。

### 响应模型

```python
from pydantic import BaseModel

class UserOut(BaseModel):
  id: int
  username: str

@app.get("/users/{uid}", response_model=UserOut)
async def get_user(uid: int):
  return {"id": uid, "username": "alice", "extra": "ignored"}
```

## 实战经验

1. 分层结构：`api / schemas / services / repositories / core(config, deps)`
2. 路由模块化：APIRouter 分领域（/users /auth /orders）再统一 include
3. 依赖注入：数据库 session（yield + finally 关闭）
4. 全局异常处理：`app.exception_handler(HTTPException)` + 统一返回格式
5. 性能：开启 uvloop + httptools（uvicorn 可选安装）
6. 日志：结构化 (loguru / structlog) + 请求链路 ID 注入
7. 鉴权：JWT / OAuth2 Password Flow（FastAPI 提供工具类）
8. 速率限制：集成 redis + 依赖层判断
9. CORS：`from fastapi.middleware.cors import CORSMiddleware`
10. 任务下发：Celery / RQ / Dramatiq + 回调更新状态

数据库集成（例如 SQLAlchemy 2.0 ORM + Async）：

```python
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/db", echo=False)
SessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_session():
  async with SessionLocal() as session:
    yield session
```

全局依赖注入：`Depends(get_session)` 注入 route。

测试：使用 `TestClient(app)`（基于 httpx）进行同步风格测试；异步直接用 `httpx.AsyncClient`。

常见性能瓶颈：数据库 N+1 / 模型转换过多 / 不必要 JSON 序列化。

## 经验总结

核心价值：类型驱动（IDE 友好 + 自动文档） + 异步并发能力。

最佳实践：

- 约束响应模型，避免内部结构泄漏
- 严格拆分 schema（In / Out / Update / DB Model）
- 利用依赖注入统一安全策略（权限 / 限流 / 事务）
- 明确长耗时操作移交任务队列（Celery）
- 接口版本化：`/api/v1` + 变更文档

不适合：长连接高吞吐自定义协议（可直接用 bare Starlette / Quart）。

## 信息参考

- FastAPI 官方: <https://fastapi.tiangolo.com>
- Starlette: <https://www.starlette.io>
- Pydantic: <https://docs.pydantic.dev>
- Uvicorn: <https://www.uvicorn.org>
- SQLAlchemy: <https://docs.sqlalchemy.org>
- OAuth2 Example: <https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/>
