---
tags:
  - tech/ops
  - type/concept
  - status/seed
description: Cisco-DevNet-Sandbox
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Docker MOC]] | [[DevOps MOC]]

---


# Cisco-DevNet-Sandbox 

## 基础概念

- 特点：官方沙箱环境、免费或开发者免费额度、无需本地镜像、适合验证 API/脚本/演示。
- 优点：马上可用（登录预约即可），无须下载 OVA，也不会有许可麻烦。
- 限制：实例是共享/受配额的，session 有时长限制，不能长期保留自定义镜像或大量并发节点。
- 如何操作（快速指南）：
    1. 注册/登录 Cisco DevNet。
    2. 在 Sandbox 页面搜索“NX-OS”“Nexus”或“Nexus 9000”相关沙箱（或“NX-API”）。
    3. 选择合适的 Sandbox，点击 “Reserve”（预约），按时间段创建 session。
    4. 连接信息（IP/用户名/密码/API URL）会在页面上显示，按文档运行你的 [connection_test.py](vscode-file://vscode-app/c:/Program%20Files/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)、[qos_api_explorer.py](vscode-file://vscode-app/c:/Program%20Files/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 即可测试。
- 建议：先用 Sandbox 做功能验证（NX-API 请求格式、YAML→命令转化、回滚逻辑），把自动化脚本在沙箱跑通后再争取长期镜像。

## 使用指南

## 实战经验

## 经验总结

## 信息参考

[Sandbox - Cisco DevNet](https://developer.cisco.com/site/sandbox/)
