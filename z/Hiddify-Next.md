---
tags:
  - tech/ops/network
  - type/howto
  - status/growing
description: Hiddify-Next
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[计算机网络 MOC]] | [[前端基础 MOC]]

---


# Hiddify-Next 简介

Hiddify-Next 是一款跨平台、多协议、现代化的代理客户端，支持 sing-box、Clash.Meta 等内核。具有操作简单、功能强大、兼容性优秀的特点，是目前（2025 年）最推荐的图形代理软件之一。

主要优势：

- 支持几乎所有现代代理协议
- 内置自动规则（中外分流）
- 自动测速、智能选择节点
- 深度整合 sing-box 核心
- UI 清爽易用
- iOS / Android / Windows / macOS 全平台覆盖

---

# 核心概念

## Profile（配置文件）

每份订阅、每个 Clash YAML、每个 sing-box JSON 都是一个 Profile。可以同时导入多个 Profile 并切换使用。

## 内核（Core）

Hiddify-Next 支持：

- **sing-box（推荐）**：最强协议支持、最高性能
- Clash.Meta：兼容 Clash 生态
- V2Ray(v2fly)：传统内核，不推荐新用户使用

## 节点（Proxy Server）

代理线路，例如：VLESS、Trojan、Shadowsocks、Hysteria2 等。

## 规则（Rules）

定义哪些流量走代理或直连，如：

- 国内直连
- 国外代理
- 局域网直连
- 流媒体指定线路

---

# 支持的协议与特性

## ✅ VLESS 系列

- VLESS TCP/TLS
- VLESS Reality（最稳定、最隐蔽）
- VLESS Vision（新版 XTLS）

## ✅ Trojan

- Trojan TLS
- Trojan-Go

## ✅ Shadowsocks

- Shadowsocks 2022（强烈推荐）
- Shadowsocks AEAD

## ✅ VMess

- VMess（支持 AEAD）

## ✅ 高速协议

- **Hysteria2（UDP 秒开）**
- **TUIC v5（高并发、超稳定）**

## ✅ 特殊协议

- ShadowTLS
- WireGuard（仅 sing-box）

## ✅ 配置格式

- Clash.Meta YAML
- sing-box JSON
- 订阅链接（推荐）
- 二维码导入

---

# 安装指南

## Windows

1. 下载 Hiddify-Next 安装包
2. 点击安装并运行
3. 建议开启开机自启
4. 启用系统代理即可开始使用

## macOS

1. 下载 dmg 文件
2. 拖入应用程序
3. 打开后授予网络权限

## Android

- 从 GitHub Release 或 Google Play 安装
- 支持剪贴板自动识别节点

## iOS / iPadOS

- App Store 搜索：`Hiddify`（官方上架版本）
- 使用系统 NEVPN 扩展，无需越狱

---

# 配置与订阅管理

## 1. 添加订阅

支持：

- 粘贴订阅链接
- 自动识别剪贴板中的节点/订阅
- 扫码导入
- 导入本地文件（Clash YAML / sing-box JSON）

步骤：

1. 打开 Profile
2. 点击 Import
3. 选择方式导入
4. 启用该配置

## 2. 更新订阅

- 支持自动更新
- 支持手动 Refresh

## 3. 多配置切换

不同机场 / 自建节点可以分成多个 Profiles，并随时切换。

---

# 代理模式与规则

Hiddify-Next 内置多种模式：

## ✅ Rule（智能模式）【推荐】

- 国内流量直连
- 国外流量代理

最符合绝大多数中国大陆/港台/新加坡用户需求。

## Global Proxy

全部走代理。

## Direct

全部直连。

## Bypass CN

排除中国流量，其余全部走代理。

## Custom

自定义规则，可导入 clash-rule-set 或 sing-box rule。

---

# 节点管理与测速

## 自动测速

支持：

- TCP Ping
- URL 测试
- 延迟统计

## 自动选择（Auto）

会根据延迟自动选择最快节点。

## 负载均衡（Load Balance）

多节点平均分流，适合：

- 游戏
- 高并发下载
- 长时间稳定连接

## 手动选择

你也可以手动切换节点。

---

# 内核说明

## ✅ sing-box（推荐）

支持所有新协议：Reality、Hysteria2、TUIC、ShadowTLS、SS2022…

优势：

- 更快、更低延迟
- 多协议支持更完整
- 更新更频繁
- 使用体验最好

## Clash.Meta

适合 Clash YAML 用户。

优点：

- 生态成熟  
	缺点：
	
- 不支持 Hysteria2 / TUIC v5
- 性能略低于 sing-box

## v2fly

传统内核，逐步退出主流。

---

# 实战经验

## ✅ 1. 最稳定的协议：VLESS + Reality

- 无证书需求
- 隐蔽性最强
- 对网络监管高度友好

## ✅ 2. 最快速的协议：Hysteria2

适合：

- 下载
- 看视频
- 游戏

## ✅ 3. 使用 Auto 节点组提升体验

智能选最快节点，适用于：

- YouTube
- Netflix
- Telegram

## ✅ 4. 避免使用 2023 年以前的旧 Clash

会出现：

- 无法连接
- 解析失败
- 不支持新协议
- 延迟过高

## ✅ 5. Windows 推荐搭配系统 TUN 模式

更兼容 Steam / 游戏 / UWP 应用。

---

# 常见问题排查

## 1. “无法连接”

检查：

- 订阅是否正常
- VPS 端口是否开放（使用 tcp.ping.pe 测试）
- 是否选择了 sing-box 内核
- 是否启用了系统代理

## 2. 某些网站打不开

- 切换到 Rule 模式
- 更新规则集
- 换节点测试

## 3. 连上但速度慢

- 测试延迟
- 换 Auto 节点组
- 尝试 Hysteria2 / TUIC 协议

## 4. iOS 上无法连接

- 检查 NEVPN 权限
- 禁用低数据模式

---

# 最佳实践总结

✅ 使用 sing-box 内核，协议支持最强  
✅ 选择 Rule 智能模式（中国直连 + 国外代理）  
✅ Reality = 最稳  
✅ Hysteria2 = 最快  
✅ 自动选择节点提升流媒体体验  
✅ 定期更新订阅  
✅ 不使用旧 Clash

--- 

# 附录：推荐工具与资源

## 官方项目

- Hiddify-Next：[https://github.com/hiddify/hiddify-next](https://github.com/hiddify/hiddify-next)
- sing-box：[https://github.com/SagerNet/sing-box](https://github.com/SagerNet/sing-box)
- Clash.Meta：[https://github.com/MetaCubeX/Clash.Meta](https://github.com/MetaCubeX/Clash.Meta)
- Xray-core：[https://github.com/XTLS/Xray-core](https://github.com/XTLS/Xray-core)

## 文档

- sing-box 文档：[https://sing-box.sagernet.org](https://sing-box.sagernet.org/)
- Clash.Meta 文档：[https://wiki.metacubex.one](https://wiki.metacubex.one/)
- Hiddify 配置工具：[https://github.com/hiddify/hiddify-config](https://github.com/hiddify/hiddify-config)
