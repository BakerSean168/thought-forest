---
tags:
  - tech/dev/frame
  - type/howto
  - status/growing
description: Electron+Vue3+Express实现本地数据同步到服务器
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Electron-MOC]] | [[Vue3 MOC]]

---


基于
- 简单的用户登录、注册功能  
- 将使用功能模块生成的对应 settings等数据以 json 格式保存在本地系统文件夹中

客户端进行操作，会修改本地相应的文件  
使用一个观察者来观察修改本地文件的操作，有修改操作则说明数据更新了；此时，观察者通知同步服务  
同步服务调用 API 将修改的文件上传至服务器数据库  

# Express

## 生成存储数据的表

```ts
// 创建数据同步表
    await connection.query(`
      CREATE TABLE IF NOT EXISTS user_data (
        id INT PRIMARY KEY AUTO_INCREMENT,
        user_id INT NOT NULL,
        FOREIGN KEY (user_id) REFERENCES users(id),
        file_name VARCHAR(255) NOT NULL CHECK (file_name != ''),
        file_content JSON,
        version INT NOT NULL DEFAULT 1,
        last_modified TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
        UNIQUE (user_id, file_name),
        INDEX idx_user_id (user_id),  
        INDEX idx_last_modified (last_modified)
      )
    `);
```

## 编写 API

期望暴露 同步接口

### 获取相应数据的 修改

# Vue3

# Express

后端

