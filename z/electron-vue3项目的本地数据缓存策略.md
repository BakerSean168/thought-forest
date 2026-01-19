---
tags:
  - tech/dev/frontend
  - type/concept
  - status/seed
description: electron-vue3项目的本地数据缓存策略
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[项目实践 MOC]] | [[后端开发 MOC]]

---


# 用户存储位置

Windows: %APPDATA%\[AppName] 或 %LOCALAPPDATA%\[AppName]  
macOS: ~/Library/Application Support/[AppName]  
Linux: ~/.config/[AppName]  

文件结构：  
```
AppData/
├── UserData/           # 用户配置和个人数据
├── Cache/              # 临时缓存数据
├── Database/           # 本地数据库文件
└── Logs/               # 日志文件
```

# 数据存储方式

使用多个SQLite数据库存储不同类型数据：  
- users.db: 用户信息
- messages.db: 聊天记录
- media.db: 媒体索引

大型二进制文件(图片、视频)单独存储在文件系统中  
自定义二进制格式存储特定数据  

# 自动登录实现

## 令牌存储

```ts
// 生成和存储令牌的示例
const generateToken = (username: string): string => {
  const tokenData = `${username}|${Date.now()}|${someUniqueDeviceId}`;
  return crypto.createHash('sha256').update(tokenData).digest('hex');
};
```

## 硬件指纹
