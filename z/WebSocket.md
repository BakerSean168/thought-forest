---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: WebSocket
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端 HTTP 请求]] | [[ECMAScript MOC]]

---


# WebSocket 

## 基础概念
### 原理

基于 TCP 的全双工通信协议，支持客户端和服务器之间的实时双向通信。

  

### 技术特点

- **协议**：WebSocket (ws:// 或 wss://)

- **连接方向**：双向

- **数据格式**：支持文本和二进制

- **浏览器支持**：现代浏览器原生支持

- **连接保持**：长连接，需要手动处理重连

  

### 优点

- 全双工通信

- 低延迟，高性能

- 支持二进制数据

- 协议开销小

- 支持压缩

  

### 缺点

- 实现复杂度较高

- 需要处理连接管理

- 某些代理/防火墙可能阻止

- 需要额外的心跳机制
## 使用指南
### 实现示例

  

**服务器端（Node.js + ws）：**

```javascript

const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

  

const clients = new Set();

  

wss.on('connection', (ws) => {

  clients.add(ws);

  ws.on('message', (message) => {

    const data = JSON.parse(message);

    handleClientMessage(data);

  });

  ws.on('close', () => {

    clients.delete(ws);

  });

  // 发送欢迎消息

  ws.send(JSON.stringify({

    type: 'connected',

    message: '连接成功'

  }));

});

  

// 广播提醒到所有客户端

function broadcastReminder(reminder) {

  const message = JSON.stringify({

    type: 'schedule-reminder',

    data: reminder

  });

  clients.forEach(client => {

    if (client.readyState === WebSocket.OPEN) {

      client.send(message);

    }

  });

}

```

  

**客户端：**

```javascript

const ws = new WebSocket('ws://localhost:8080');

  

ws.onopen = () => {

  console.log('WebSocket 连接已建立');

};

  

ws.onmessage = (event) => {

  const message = JSON.parse(event.data);

  switch (message.type) {

    case 'schedule-reminder':

      showNotification(message.data);

      break;

    case 'heartbeat':

      // 响应心跳

      ws.send(JSON.stringify({ type: 'pong' }));

      break;

  }

};

  

ws.onclose = () => {

  console.log('WebSocket 连接已断开，尝试重连...');

  setTimeout(connectWebSocket, 5000);

};

  

// 发送消息到服务器

function sendMessage(type, data) {

  if (ws.readyState === WebSocket.OPEN) {

    ws.send(JSON.stringify({ type, data }));

  }

}

```

  

### 适用场景

- 实时聊天应用

- 在线游戏

- 协作编辑

- 实时交易系统

- 双向数据同步
## 实战经验
## 经验总结
## 信息参考

