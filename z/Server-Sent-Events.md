---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Server-Sent-Events
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端 HTTP 请求]] | [[ECMAScript MOC]]

---


# Server-Sent-Events 

## 基础概念
### 原理

基于 HTTP/1.1 的单向通信技术，服务器可以主动向客户端推送数据。

### 技术特点

- **协议**：基于 HTTP

- **连接方向**：单向（服务器 → 客户端）

- **数据格式**：文本格式，支持 JSON

- **浏览器支持**：现代浏览器原生支持

- **自动重连**：浏览器自动处理断线重连

  

### 优点

- 实现简单，无需额外库

- 浏览器原生支持，自动重连

- 支持跨域（CORS）

- 服务器端实现简单

- 适合单向推送场景

  

### 缺点

- 只支持单向通信

- 连接数限制（浏览器对同域名连接数有限制）

- 不支持二进制数据

- 某些代理服务器可能不支持
## 使用指南

### 实现示例

**服务器端（Node.js/Express）：**

```javascript

app.get('/api/events', (req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive',
    'Access-Control-Allow-Origin': '*'
  });

  

  // 发送事件
  const sendEvent = (eventType, data) => {
    res.write(`event: ${eventType}\n`);
    res.write(`data: ${JSON.stringify(data)}\n\n`);
  };

  

  // 定时发送心跳
  const heartbeat = setInterval(() => {
    sendEvent('heartbeat', { timestamp: Date.now() });
  }, 30000);

  

  // 处理断开连接
  req.on('close', () => {
    clearInterval(heartbeat);
  });

});

```

  

**客户端：**

```javascript

const eventSource = new EventSource('/api/events');

  

eventSource.addEventListener('schedule-reminder', (event) => {
  const data = JSON.parse(event.data);
  showNotification(data);
});

  

eventSource.onopen = () => {
  console.log('SSE 连接已建立');
};

  

eventSource.onerror = (error) => {
  console.error('SSE 连接错误:', error);
};

```

  

### 适用场景

- 实时通知推送

- 日志监控

- 股票价格更新

- 聊天消息（单向）

- 进度更新
## 实战经验
## 经验总结
## 信息参考

