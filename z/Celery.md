---
tags:
  - tech/lang/python
  - type/concept
  - status/seed
description: Celery分布式任务队列框架基础
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Python MOC]] | [[后端框架 MOC]]

---


# Celery

## 基础概念

核心组件：
1. Broker：消息中间件（Redis / RabbitMQ / Redis Streams 等）
2. Worker：执行任务进程（并发：prefork / gevent / eventlet / solo）
3. Backend：存储任务结果（Redis / DB / RPC）
4. Task：函数封装，支持重试、超时、序列化
5. Beat：定时任务调度器

特性：
- 支持任务重试（指数退避）
- 任务路由（queue / exchange / routing_key）
- 任务链（chain）、和弦（chord）、分组（group）、映射（map / starmap）
- 结果忽略 / 持久化可选（提高性能）
- 软超时 / 硬超时
- 信号机制（prerun / postrun / task_failure）

适用场景：发送邮件、批量数据处理、耗时第三方 API 调用、图像/音视频处理、异步通知、定时清理、工作流编排。

## 使用指南

### 安装
```bash
pip install celery[redis]
```

### 定义与启动
```python
# app.py
from celery import Celery

celery_app = Celery(
  'demo',
  broker='redis://127.0.0.1:6379/1',
  backend='redis://127.0.0.1:6379/2'
)

@celery_app.task(bind=True, acks_late=True, autoretry_for=(Exception,), retry_backoff=2, max_retries=5)
def add(self, a: int, b: int):
  return a + b

if __name__ == '__main__':
  celery_app.worker_main(['worker', '-l', 'info'])
```

启动 Worker：
```bash
celery -A app.celery_app worker -l info -Q default -c 4
```

调用任务：
```python
from app import add
res = add.delay(2, 3)
print(res.id, res.ready(), res.get(timeout=5))
```

### 任务组合
```python
from celery import chain, group, chord

# 串行
chain(add.s(1,2), add.s(3))()

# 并行
group(add.s(i, i) for i in range(5))()

# 和弦：前面并行，回调汇总
chord(group(add.s(i, i) for i in range(5)))(add.s(10))
```

### 定时任务（Beat）
`celery -A app.celery_app beat -l info`

配置周期任务（celeryconfig.py）：
```python
from celery.schedules import crontab
beat_schedule = {
  'daily-report': {
   'task': 'app.add',
   'schedule': crontab(hour=2, minute=0),
   'args': (1,2)
  }
}
timezone = 'Asia/Shanghai'
```

### 并发模式选择
- prefork：CPU 密集 / 通用
- gevent / eventlet：高 IO 并发（需 monkey patch）
- solo：调试

### 优雅关闭与幂等
- 避免任务副作用不可逆，确保可重放（幂等 key）
- 使用 `acks_late=True` + 手动控制异常抛出

### 超时与重试
```python
@celery_app.task(soft_time_limit=10, time_limit=20, autoretry_for=(TimeoutError,), retry_backoff=True)
def slow_job(): ...
```

## 实战经验

1. 任务幂等：数据库唯一约束 / Redis SETNX 标记
2. 避免传递超大参数：传引用 ID -> 后端再查询
3. 任务拆分：长任务拆子任务 + 汇总（chord）
4. 监控：flower / Prometheus metrics + backlog 长度
5. 序列化：默认 JSON；复杂对象需转基础结构
6. 限速：`task_rate_limit='10/m'`
7. 优先级：RabbitMQ x-max-priority；Redis 支持有限（需多个队列替代）
8. 压力控制：合理设置 prefetch multiplier（默认4）
9. 避免死信堆积：配置任务过期（expires / visibility_timeout）
10. 链路追踪：task_id 传递下游日志

常见问题：
- 任务重复执行：worker 重启 / ack 丢失 -> 幂等必做
- 结果 backend 膨胀：配置 `result_expires` + 清理任务结果
- 队列消息挤压：拆分为多个 queue，按业务隔离

## 经验总结

Celery 适合作为“异步工作者池 + 调度编排”底座。针对超大规模与高吞吐事件流推荐 Kafka + 专用消费者服务。

控制点：幂等性 / 可观测性 / 任务粒度 / 超时重试策略。

替代比较：
- Dramatiq：更轻，类型提示友好
- RQ：简单 Redis 队列，不含复杂工作流
- Airflow：面向有向任务调度（批处理），非实时

## 信息参考

- 官方: <https://docs.celeryq.dev>
- Flower: <https://flower.readthedocs.io>
- 最佳实践: <https://docs.celeryq.dev/en/stable/userguide/tasks.html>
- 重试策略: <https://docs.celeryq.dev/en/stable/userguide/tasks.html#retrying>

