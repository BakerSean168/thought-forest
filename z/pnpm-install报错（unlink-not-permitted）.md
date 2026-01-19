---
tags:
  - tech/lang/nodejs
  - type/howto
  - status/evergreen
description: pnpm install报错unlink-not-permitted解决方案
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Node.js MOC]] | [[问题汇总]]

---


# pnpm-install报错（unlink-not-permitted） 

## 问题详情

换了新电脑，重新安装依赖的时候报错了

```
EPERM: operation not permitted, unlink 'D:\workProgram\olp-admin\node_modules\.pnpm\escape-string-regexp@4.0.0\node_modules\escape-string-regexp\readme.md'
```
## 解决方案

关掉 VSCode，在终端（PowerShell-管理员权限）中打开项目，使用 `pnpm install`
## 经验总结

导致的原因： 
>VSCode was locking some files. Potentially something else could be locking these files for you.

>In my case, Process Explorer invariably tells me that the culprit locking the files `npm` is trying to delete is… another `node.exe` process spawned by `npm` running `npm`! Oh joy, this tool never ceases to surprise… (this is on Windows 10, Node 12.11.0, npm 6.11.3)
## 信息参考

[npm - Error: EPERM: operation not permitted, unlink 'D:\Sources\**\node_modules\fsevents\node_modules\abbrev\package.json' - Stack Overflow](https://stackoverflow.com/questions/46020018/error-eperm-operation-not-permitted-unlink-d-sources-node-modules-fseven)
