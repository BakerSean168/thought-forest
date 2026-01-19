---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Push-API+Service-Worker
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# Push-API+Service-Worker 

## 基础概念
### 原理

通过浏览器的推送服务和 Service Worker 实现离线推送。

  

### 技术特点

- **离线支持**：即使页面关闭也能接收

- **系统集成**：操作系统级别的通知

- **权限要求**：需要用户授权

- **服务商依赖**：依赖推送服务提供商

  

### 优点

- 支持离线推送

- 系统级通知

- 用户体验好

- 节省服务器资源

  

### 缺点

- 设置复杂

- 依赖第三方服务

- 权限限制

- 调试困难
## 使用指南
### 实现示例

  

**Service Worker：**

```javascript

// sw.js

self.addEventListener('push', (event) => {

  const data = event.data?.json() || {};

  event.waitUntil(

    self.registration.showNotification(data.title || '提醒', {

      body: data.body,

      icon: '/icon-192.png',

      tag: data.tag || 'default',

      data: data.payload

    })

  );

});

```

  

**客户端注册：**

```javascript

// 注册 Service Worker 和订阅推送

async function setupPushNotifications() {

  if ('serviceWorker' in navigator && 'PushManager' in window) {

    const registration = await navigator.serviceWorker.register('/sw.js');

    const permission = await Notification.requestPermission();

    if (permission === 'granted') {

      const subscription = await registration.pushManager.subscribe({

        userVisibleOnly: true,

        applicationServerKey: urlBase64ToUint8Array(publicKey)

      });

      // 将订阅信息发送到服务器

      await fetch('/api/push/subscribe', {

        method: 'POST',

        headers: { 'Content-Type': 'application/json' },

        body: JSON.stringify(subscription)

      });

    }

  }

}

```

  

### 适用场景

- 重要通知推送

- 离线消息提醒

- 定时提醒应用

- 新闻推送
## 实战经验
## 经验总结
## 信息参考

