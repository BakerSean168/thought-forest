---
tags:
  - life/work
  - type/howto
  - status/growing
description: 消息中心问题（侧边栏按钮显示问题and邮件未收到）
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[生活 MOC]] | [[职业发展 MOC]]

---


# 消息中心问题

## 问题详情

侧边栏按钮显示问题（没有证书以及考试的消息分类显示）and邮件未收到）

## 基础信息

**侧边栏组件收到的link对象**

```ts
[
    {
        "type": 1,
        "bizType": 6,
        "count": 45,
        "unreadCount": 39
    },
    {
        "type": 1,
        "bizType": 7,
        "count": 21,
        "unreadCount": 20
    },
    {
        "type": 1,
        "bizType": 3,
        "count": 40,
        "unreadCount": 40
    },
    {
        "type": 1,
        "bizType": 4,
        "count": 2,
        "unreadCount": 2
    },
    {
        "type": 1,
        "bizType": 5,
        "count": 1,
        "unreadCount": 0
    }
]
```

**`app-api/system/notify-message/my-bizTaype`接口返回的信息**

```ts
{
    "code": 0,
    "data": [
        {
            "type": 1,
            "bizType": 6,
            "count": 45,
            "unreadCount": 40
        },
        {
            "type": 1,
            "bizType": 7,
            "count": 21,
            "unreadCount": 20
        },
        {
            "type": 1,
            "bizType": 3,
            "count": 40,
            "unreadCount": 40
        },
        {
            "type": 1,
            "bizType": 4,
            "count": 2,
            "unreadCount": 2
        },
        {
            "type": 1,
            "bizType": 5,
            "count": 1,
            "unreadCount": 0
        },
        {
            "type": 3,
            "bizType": 15,
            "count": 1,
            "unreadCount": 0
        }
    ],
    "msg": ""
}
```

有 1 类型 和 3类型 两大类，然后又有 bizType（业务类型） 属性，前端有 bizType 的枚举 label。

![[Pasted image 20251015145816.png]]

## 解决方案

### 初步确认

**按钮没有问题**：
大类 1 是在线学院，并且业务类型 1-8 已经有对应。
后端先要增加返回的 link（证书和考试的），还有明确 大类 3 和 业务类型 15 是啥

这是后端定义的业务大类与小类对应关系：
```txt
1. 在线学院大类（type=1）
在线学院证书生成通知：type=1，bizType=1
在线学院等待列表日期匹配通知：type=1，bizType=2
在线学院课堂结束通知：type=1，bizType=3
在线学院课堂取消通知：type=1，bizType=4
在线学院课堂合并通知：type=1，bizType=5
在线学院课堂发布通知：type=1，bizType=6
在线学院预定审批通过通知：type=1，bizType=7
在线学院预定审批拒绝通知：type=1，bizType=8
在线学院预定审批通知：type=1，bizType=9
在线学院课堂更新通知：type=1，bizType=10
在线学院课堂等待列添加通知：type=1，bizType=11
2. EDP 大类（type=2）
EDP 学习地图发布通知：type=2，bizType=1
EDP TNI 审批通知：type=2，bizType=2
3. LEARNING 模块大类（type=3）
LEARNING 模块课程分配通知：type=3，bizType=14
LEARNING 模块考试分配通知：type=3，bizType=15
4. 直播大类（type=4）
直播直播间发布通知：type=4，bizType=16
5. 系统模块大类（type=5）
系统模块问卷发布通知：type=5，bizType=17
```


**未收到邮件**：
应该是后端问题吧，我不清楚

## 相关信息

[BUG #1709 Message Center-消息中心信息优化 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1709.html)

[BUG #1710 Message Center-消息中心信息优化 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1710.html)
