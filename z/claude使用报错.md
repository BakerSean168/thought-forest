---
tags:
  - tech/ai/claude
  - type/howto
  - status/seed
description: Claude AI使用过程中的报错问题记录
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[问题汇总]]

---


# claude使用报错 

## 问题详情

```cmd
Unable to connect to Anthropic services Failed to connect to api.anthropic.com: ERR_BAD_REQUEST Please check your internet connection and network settings. Note: Claude Code might not be available in your country. Check supported countries at
```

使用了 clash 代理仍然没有效果
## 解决方案

以上问题产生一般是因为网络和地区

### 修改系统地区，语言？

### `.claude.json`配置

`"hasCompletedOnboarding": true` 添加这一行代码

```json
{
  "numStartups": 2,
  "installMethod": "global",
  "autoUpdates": true,
  "tipsHistory": {
    "new-user-warmup": 2,
    "plan-mode-for-complex-tasks": 2
  },
  "cachedStatsigGates": {
    "tengu_disable_bypass_permissions_mode": false,
    "tengu_tool_pear": false
  },
  "cachedDynamicConfigs": {
    "tengu-top-of-feed-tip": {
      "tip": "",
      "color": ""
    }
  },
  "firstStartTime": "",
  "userID": "",
  "projects": {
  },
  "hasCompletedOnboarding": true // 添加这一行代码
}
```

我添加完上面的后，能够正确登录，正确连接了，但是在发送问题后会报错  

`API Error: 403 {"error":{"type":"forbidden","message":"Request not allowed"}} · Please run /login`

## 深度代理

clash 服务模式+TUN模式

## 信息参考

[Clash代理体验Claude](https://zhuanlan.zhihu.com/p/1931349720892183868)

