---
tags:
  - tech/app/electron
  - type/howto
  - status/growing
description: README
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Resource]] | [[生活 MOC]]

---

# Electron端

## 基础概念

[[Electron]]

Electron 端应该是一个能最大程度支持离线操作，本地数据存储，本地数据计算的一个桌面应用程序。
- 大部分 api 中的服务都应该移植到 electron 的主进程中，使用 electron 中的系统接口，目前 ai 服务应该是还得用 api 端的（后续期望能够有本地大模型来实现）。（应该只需要在 api 中新增一个 数据同步服务，来提供本地与 web 端的数据同步）
- web中的代码则移植到 electron 的渲染进程中
- 两者通过进程间通信 ipc 来进行数据传输
- 应该能支持离线使用
