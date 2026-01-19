---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Fetch API - 现代浏览器原生 HTTP 请求接口
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[前端 HTTP 请求]] | [[ECMAScript MOC]]

---

# Fetch API

> Fetch API 是现代浏览器提供的原生 HTTP 请求接口，基于 Promise，语法简洁优雅。

---

## 🎯 基本用法

### GET 请求

```javascript
// 基础用法
fetch('/api/users')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));

// async/await 语法
async function getUsers() {
  try {
    const response = await fetch('/api/users');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
  }
}
```

### POST 请求

```javascript
async function createUser(userData) {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(userData),
  });
  
  return response.json();
}

// 使用
createUser({ name: 'John', email: 'john@example.com' });
```

### 其他请求方法

```javascript
// PUT
await fetch('/api/users/1', {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Updated Name' }),
});

// DELETE
await fetch('/api/users/1', {
  method: 'DELETE',
});

// PATCH
await fetch('/api/users/1', {
  method: 'PATCH',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Patched Name' }),
});
```

---

## 📋 Response 对象

```javascript
const response = await fetch('/api/data');

// 属性
response.ok        // 状态码 200-299 时为 true
response.status    // HTTP 状态码，如 200
response.statusText // 状态文本，如 "OK"
response.headers   // Headers 对象
response.url       // 请求的 URL

// 读取响应体的方法（只能调用一次）
response.json()    // 解析为 JSON
response.text()    // 解析为文本
response.blob()    // 解析为 Blob
response.arrayBuffer() // 解析为 ArrayBuffer
response.formData() // 解析为 FormData
```

---

## ⚠️ 错误处理

**重要**：Fetch 不会自动拒绝 HTTP 错误状态码！

```javascript
async function fetchWithErrorHandling(url) {
  try {
    const response = await fetch(url);
    
    // 需要手动检查状态码
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    return await response.json();
  } catch (error) {
    if (error.name === 'TypeError') {
      // 网络错误（无法连接服务器）
      console.error('Network error:', error);
    } else {
      // HTTP 错误或其他错误
      console.error('Fetch error:', error);
    }
    throw error;
  }
}
```

---

## 🚫 取消请求

使用 `AbortController` 取消请求：

```javascript
const controller = new AbortController();
const signal = controller.signal;

// 发起请求
fetch('/api/data', { signal })
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Request was cancelled');
    }
  });

// 取消请求
controller.abort();

// 带超时的请求
async function fetchWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const id = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, { signal: controller.signal });
    clearTimeout(id);
    return response;
  } catch (error) {
    clearTimeout(id);
    throw error;
  }
}
```

---

## 📤 上传文件

```javascript
// FormData 上传
async function uploadFile(file) {
  const formData = new FormData();
  formData.append('file', file);
  
  const response = await fetch('/api/upload', {
    method: 'POST',
    body: formData,
    // 注意：不要设置 Content-Type，浏览器会自动设置
  });
  
  return response.json();
}
```

---

## 🔄 与 Axios 对比

| 特性 | Fetch | Axios |
|------|-------|-------|
| 浏览器支持 | 现代浏览器 | 需要引入库 |
| Promise | ✅ 原生 | ✅ |
| 自动 JSON 转换 | ❌ 需手动 | ✅ |
| HTTP 错误处理 | ❌ 需手动 | ✅ 自动抛出 |
| 拦截器 | ❌ | ✅ |
| 取消请求 | AbortController | CancelToken |
| 请求进度 | ❌ | ✅ |
| Node.js | 需要 node-fetch | ✅ |

---

## 🔗 相关笔记

- [[前端 HTTP 请求]] - HTTP 请求技术总览
- [[axios]] - Axios HTTP 客户端
- [[前端HTTP状态码的处理]] - HTTP 状态码处理
- [[前端接收流式数据的方案]] - 流式数据处理
