---
tags: [type/howto, status/evergreen]
description: 如何xxx
created: 2026-01-01T18:30:52
updated: 2026-01-02T00:06:46
---

# Tailscale 

## Tailscale 配置

根据你提供的操作记录和截图信息，在 ImmortalWrt (OpenWrt) 上配置 Tailscale 的完整流程可以总结为以下四个阶段：

### 1. 环境准备与依赖安装

在正式安装前，需要确保系统具备运行条件。

- **检查内核模块**：确认系统存在 `/dev/net/tun` 设备。
- **确认存储空间**：通过软件包管理界面确认 `/overlay` 仍有足够空间（安装后你仍剩余约 109.16 MiB）。
- **安装必要依赖**：由于新版固件默认使用 nftables，需安装 `iptables-nft` 和 `ip6tables-nft` 以便 Tailscale 配置防火墙规则。
- **安装主程序**：使用 `opkg install tailscale` 完成软件安装。

---

### 2. 服务启动与账号认证

安装完成后，需要让设备接入你的 Tailscale 网络。

- **启动后台服务**：执行 `/etc/init.d/tailscale start` 启动守护进程 `tailscaled`。
- **获取登录链接**：执行 `tailscale up`，控制台会生成一个 OAuth 认证链接。
- **身份验证**：在浏览器打开链接，使用 **GitHub** 或其他 SSO 账号登录并授权。
- **确认状态**：认证成功后，设备会获得一个 `100.x.y.z` 的内部 IP（如你的设备 IP 为 `100.121.255.124`）。

---

### 3. 实现远程访问内网（子网路由）

为了能让远程设备访问路由器下方的其他设备，需要配置子网路由。

- **宣告路由**：执行命令 `tailscale up --advertise-routes=192.168.0.0/24 --accept-dns=false`。
	- `--advertise-routes` 告知网络你可以转发前往 `192.168.0.x` 的流量。
	- `--accept-dns=false` 防止 Tailscale 修改路由器的 DNS 设置，避免干扰局域网正常上网。
- **后台审批**：在 Tailscale 控制台的设备设置中（Edit route settings），手动勾选并开启对应的子网路由。

---

### 4. 性能优化与多端接入

- **UDP 性能优化**：针对 `UDP GRO` 警告，需安装 `ethtool` 并针对 `br-lan` 接口开启硬件加速，以提升数据转发效率。
- **多端互联**：在 Windows、Android 或 iOS 客户端安装 Tailscale 并登录同一账号，实现点对点安全连接。

---

**目前你的配置已经基本完成。需要我帮你写一个自动优化 UDP GRO 性能的启动脚本，并教你如何验证远程设备是否能成功 ping 通家里的内网 IP 吗？**