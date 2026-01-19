---
tags:
  - status/growing
  - type/debug
  - tech/dev/frontend
description: base_template
created: 2025-12-15T16:30:48
updated: 2025-12-15T16:32:35
project: krjx-olp
---

# trainer通过管理端跳转虚拟课堂直播角色功能失效问题 

有 join api
涌棋哥封装了一个 接口，用于区分 MODERATOR or VIEWER，但是需要传参。现在 academy 中没有生效，应该是对应后端没有给那个接口传需要的参数。