---
tags:
  - tech/ops/proxy
  - type/concept
  - status/growing
description: V2Ray代理软件基础概念
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---

好的，请看 V2Ray 的基础概念补充：

## 基础概念

V2Ray 是一个模块化的代理软件包（纯命令行工具），可以用来构建专用的网络。它的一些核心概念对于理解其工作原理至关重要。

*   **Inbound (传入连接):** 所有进入 V2Ray 的流量连接。它可以是来自你本机浏览器的连接，也可以是来自局域网内其他设备的连接。Inbound 负责接收这些流量并将其交给 V2Ray 内核进行处理。你可以配置多个 Inbound 来监听不同的端口，使用不同的协议。

*   **Outbound (传出连接):** 所有离开 V2Ray 的流量连接。在 V2Ray 处理完来自 Inbound 的流量后，会根据你的配置决定将流量发送到哪里。Outbound 就是这个出口，它决定了流量最终将通过什么协议、发送到哪个服务器。最常见的 Outbound 就是将流量发送到远程的 V2Ray 服务器。

*   **Protocol (协议):** 这是 V2Ray 客户端与服务器之间通信时使用的语言。V2Ray 支持多种协议，其中最核心的是 **VMess** 协议。此外，它也兼容 **Shadowsocks**、**SOCKS**、**HTTP** 等多种协议。选择不同的协议会影响连接的性能、稳定性和抗审查能力。

*   **Routing (路由):** 路由模块是 V2Ray 内部的交通指挥官。当 V2Ray 接收到流量后，路由模块会根据你设定的规则来判断这个流量应该走哪个 Outbound。你可以设置非常灵活的规则，例如：
    *   访问国内网站时，直接连接（不走代理）。
    *   访问国外网站时，通过指定的 V2Ray 服务器连接。
    *   拦截广告网站的连接。
    *   特定应用（如 Telegram）的流量走特定的代理。

*   **Transport (传输):** 传输协议决定了 V2Ray 的数据包是如何在网络中进行伪装和传输的。这对于绕过网络审查和防火墙至关重要。常见的传输协议有：
    *   **TCP:** 标准的 TCP 连接。
    *   **mKCP:** 一种基于 KCP 协议的传输方式，在网络状况不佳时（如丢包严重）有更好的表现。
    *   **WebSocket (WS):** 将流量伪装成标准的 WebSocket 流量，可以与 Nginx、Caddy 等 Web 服务器结合使用，隐蔽性好。
    *   **HTTP/2 (h2):** 将流量伪装成 HTTP/2 流量，同样具有很好的隐蔽性。
    *   **QUIC:** 基于 UDP 的新一代传输协议，连接速度快。

*   **DNS:** V2Ray 内置了一个 DNS 服务器。通过使用这个内部 DNS，可以防止 DNS 查询被污染或泄露，从而提高安全性和隐私保护。你可以配置 V2Ray 使用特定的 DNS 服务器（如 Google 的 8.8.8.8 或 Cloudflare 的 1.1.1.1）来解析域名。

**简单总结一下工作流程：**

`你的应用程序 (如浏览器)` -> `Inbound (V2Ray 接收流量)` -> `Routing (V2Ray 判断流量去向)` -> `Outbound (V2Ray 将流量通过指定协议和传输方式发出)` -> `目标网站或服务`

## 实战经验

使用 V2RayN 作为图形化界面工具
## 信息参考

*   **Project V 官方网站:** [https://www.v2fly.org/](https://www.v2fly.org/)
*   **V2Ray 配置指南:** [https://guide.v2fly.org/](https://guide.v2fly.org/)

希望这些信息对你有帮助！

