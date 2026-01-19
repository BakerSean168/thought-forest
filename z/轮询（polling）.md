---
tags:
  - tech/lang/web
  - type/concept
  - status/growing
description: 轮询(Polling)前后端通信原理与实现
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[前后端实时通信]] | [[API开发 MOC]]

---


# 轮询（polling） 

## 基础概念
### 原理

客户端定时向服务器发送请求检查是否有新数据。

  

### 技术特点

- **协议**：HTTP

- **连接方向**：客户端主动

- **实时性**：取决于轮询间隔

- **浏览器支持**：完全兼容

- **服务器压力**：相对较高

  

### 优点

- 实现简单

- 兼容性极好

- 服务器无状态

- 易于调试和监控

  

### 缺点

- 实时性差

- 资源浪费（无效请求）

- 服务器压力大

- 网络开销大
## 使用指南
### 实现示例

  

**服务器端：**

```javascript

// API 端点：获取最新提醒

app.get('/api/reminders/latest', (req, res) => {

  const lastCheck = req.query.since || 0;

  const newReminders = getRemindersAfter(lastCheck);

  res.json({

    reminders: newReminders,

    timestamp: Date.now()

  });

});

```

  

**客户端：**

```javascript

let lastCheckTime = 0;

  

function pollForReminders() {

  fetch(`/api/reminders/latest?since=${lastCheckTime}`)

    .then(response => response.json())

    .then(data => {

      if (data.reminders.length > 0) {

        data.reminders.forEach(reminder => {

          showNotification(reminder);

        });

      }

      lastCheckTime = data.timestamp;

    })

    .catch(error => {

      console.error('轮询失败:', error);

    })

    .finally(() => {

      // 下次轮询

      setTimeout(pollForReminders, 5000); // 5秒间隔

    });

}

  

// 启动轮询

pollForReminders();

```

  

### 适用场景

- 对实时性要求不高的场景

- 简单的状态检查

- 老旧系统的兼容方案
## 实战经验
## 经验总结
## 信息参考

