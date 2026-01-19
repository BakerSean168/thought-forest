---
tags:
  - tech/dev/frontend
  - type/concept
  - status/growing
description: 长轮询 (Long-Polling)
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[前端 HTTP 请求]] | [[前端基础 MOC]]

---


# 长轮询 (Long-Polling) 

## 基础概念
### 原理

客户端发送请求后，服务器不立即响应，而是保持连接直到有数据或超时。

  

### 技术特点

- **协议**：HTTP

- **连接保持**：服务器端保持连接

- **实时性**：接近实时

- **浏览器支持**：完全兼容

  

### 优点

- 实时性好

- 兼容性好

- 减少无效请求

- 服务器可控制推送时机

  

### 缺点

- 服务器需要管理长连接

- 超时处理复杂

- 并发连接数限制

- 某些代理可能超时
## 使用指南
### 实现示例

  

**服务器端：**

```javascript

const pendingRequests = new Map();

  

app.get('/api/reminders/wait', (req, res) => {

  const clientId = req.query.clientId || generateClientId();

  const timeout = 30000; // 30秒超时

  // 设置超时

  const timeoutId = setTimeout(() => {

    pendingRequests.delete(clientId);

    res.json({ type: 'timeout', reminders: [] });

  }, timeout);

  // 保存待处理的请求

  pendingRequests.set(clientId, { res, timeoutId });

  // 处理客户端断开连接

  req.on('close', () => {

    const pending = pendingRequests.get(clientId);

    if (pending) {

      clearTimeout(pending.timeoutId);

      pendingRequests.delete(clientId);

    }

  });

});

  

// 当有新提醒时，推送给等待的客户端

function pushReminderToClients(reminder) {

  pendingRequests.forEach((pending, clientId) => {

    clearTimeout(pending.timeoutId);

    pending.res.json({

      type: 'reminder',

      reminders: [reminder]

    });

    pendingRequests.delete(clientId);

  });

}

```

  

**客户端：**

```javascript

const clientId = generateClientId();

  

function longPoll() {

  fetch(`/api/reminders/wait?clientId=${clientId}`)

    .then(response => response.json())

    .then(data => {

      if (data.type === 'reminder') {

        data.reminders.forEach(reminder => {

          showNotification(reminder);

        });

      }

    })

    .catch(error => {

      console.error('长轮询失败:', error);

    })

    .finally(() => {

      // 立即开始下一次长轮询

      setTimeout(longPoll, 1000);

    });

}

  

// 启动长轮询

longPoll();

```

  

### 适用场景

- 需要准实时通信但不支持 WebSocket 的环境

- 通知系统

- 简单的聊天应用
## 实战经验
## 经验总结
## 信息参考

