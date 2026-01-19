---
tags:
  - tech/lang/typescript
  - type/howto
  - status/evergreen
description: tsc编译器缓存导致文件大小写报错
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
moc/parent: "TypeScript MOC"

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


### ts 编译器缓存，导致文件大小写报错

`cd packages/domain-server && Remove-Item -Recurse -Force node_modules -ErrorAction SilentlyContinue; Remove-Item -Recurse -Force dist -ErrorAction SilentlyContinue`
用上面的命令清除缓存再 install

