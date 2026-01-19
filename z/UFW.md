---
tags:
  - tech/ops/linux
  - type/howto
  - status/growing
description: UFW Linux防火墙配置指南
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---


## 🛡️ UFW

UFW，全称 **U**ncomplicated **F**ire**w**all，是 Ubuntu/Debian 等 Linux 发行版上默认提供的防火墙管理工具。它的设计目标是简化 `iptables` 复杂的命令行操作，让用户能够更轻松地配置和管理防火墙规则。

-----

## 基础概念

  * **什么是 UFW？**
	  * UFW 是 `iptables` 的前端（front-end）。它通过更直观的命令来生成和管理复杂的 `iptables` 规则集。
	  * 它旨在使防火墙管理对于系统管理员和普通用户来说都**简单**（Uncomplicated）。
  * **默认策略 (Default Policies):**
	  * UFW 默认有两种基本策略：
		  * **进站 (Incoming):** 默认设置为 **DENY**（拒绝）。这意味着除非显式允许，所有从外部进入系统的连接都会被丢弃。
		  * **出站 (Outgoing):** 默认设置为 **ALLOW**（允许）。这意味着您的系统可以自由地发起对外部的连接。
		  * **转发 (Forward):** 用于路由的情况，默认为 **DENY**。
  * **状态 (Status):**
	  * UFW 可以处于三种状态：
		  * **Inactive (非活动):** 防火墙完全关闭，不执行任何规则。
		  * **Active (活动):** 防火墙正在运行，并根据规则集进行过滤。
		  * **Logging (日志):** 用于记录被规则捕获到的连接尝试。
  * **动作 (Actions):**
	  * **Allow (允许):** 允许连接通过。
	  * **Deny (拒绝):** 静默丢弃连接，不回复发送方。
	  * **Reject (拒绝):** 丢弃连接，并向发送方发送一个错误消息（如 TCP RST 或 ICMP Port Unreachable）。
	  * **Limit (限制):** 允许连接，但限制从同一 IP 地址在短时间内尝试连接的次数（用于防范暴力破解）。

-----

## 🛠️ 使用指南

### 1\. 状态管理

| 命令 | 描述 |
| :--- | :--- |
| `sudo ufw status` | **查看防火墙的当前状态和已配置的规则。** |
| `sudo ufw status verbose` | 查看详细状态，包括默认策略和日志级别。 |
| `sudo ufw enable` | **激活** UFW。**注意：执行前请确保已允许 SSH 端口，否则可能被锁在服务器外！** |
| `sudo ufw disable` | **停用** UFW。 |
| `sudo ufw reset` | 将 UFW 规则重置为默认设置（禁用状态且所有规则清除）。 |

### 2\. 配置默认策略

```bash
# 拒绝所有进入的连接 (最安全的默认设置)
sudo ufw default deny incoming

# 允许所有出去的连接
sudo ufw default allow outgoing
```

### 3\. 添加规则

你可以通过端口号、服务名称或协议来指定规则。

| 类型 | 命令示例 | 描述 |
| :--- | :--- | :--- |
| **按服务名** | `sudo ufw allow ssh` | 允许 SSH (默认端口 22)。 |
| **按端口号** | `sudo ufw allow 80` | 允许 TCP 端口 80 (HTTP)。 |
| **指定协议** | `sudo ufw allow 443/tcp` | 允许 TCP 端口 443 (HTTPS)。 |
| | `sudo ufw allow 53/udp` | 允许 UDP 端口 53 (DNS)。 |
| **指定IP** | `sudo ufw allow from 192.168.1.100` | 仅允许来自特定 IP 的所有连接。 |
| **指定接口** | `sudo ufw allow in on eth0 to any port 22` | 仅允许在 `eth0` 接口上进入的 SSH 连接。 |
| **端口范围** | `sudo ufw allow 6000:6007/tcp` | 允许从端口 6000 到 6007 的 TCP 连接。 |
| **限制** | `sudo ufw limit ssh` | 限制 SSH 连接：如果一个 IP 地址在 30 秒内尝试连接超过 6 次，则会被阻止。 |

### 4\. 删除规则

使用 `status numbered` 查看规则列表，然后使用编号删除。

```bash
# 查看带编号的规则列表
sudo ufw status numbered 

# 删除编号为 3 的规则
sudo ufw delete 3
```

*或者*，直接在删除命令中重复原始的 `allow`/`deny` 规则。

```bash
# 删除允许端口 80 的规则
sudo ufw delete allow 80
```

-----

## 🚀 实战经验

### 1\. 初始服务器配置（必备三步）

在任何服务器上设置 UFW 的最基本和最关键的步骤：

1.  **允许 SSH：** **必须**在激活防火墙之前执行，以防被锁定。

	```bash
    sudo ufw allow ssh
    ```

2.  **设置默认拒绝策略：** 默认情况下阻止所有进入的流量。

	```bash
    sudo ufw default deny incoming
    ```

3.  **激活防火墙：**

	```bash
    sudo ufw enable
    ```

### 2\. 网站服务器配置

| 目的 | 命令 |
| :--- | :--- |
| **HTTP (80)** | `sudo ufw allow http` 或 `sudo ufw allow 80` |
| **HTTPS (443)** | `sudo ufw allow https` 或 `sudo ufw allow 443` |
| **MySQL/PostgreSQL** | `sudo ufw allow from 192.168.1.0/24 to any port 3306` (仅允许内网访问) |

### 3\. 日志管理

启用日志可以帮助你调试或监控入侵尝试。

```bash
# 启用日志 (Low, Medium, High, Full)
sudo ufw logging on

# 查看日志文件 (通常位于此路径)
tail -f /var/log/ufw.log
```

-----

## 📝 经验总结

  * **安全第一原则：** 永远先设置 **`deny incoming`**，再 **`allow`** 所需的端口和服务。
  * **SSH 预防措施：** 在执行 `sudo ufw enable` **之前**，请务必确认 `sudo ufw allow ssh`（或你的自定义 SSH 端口）已成功添加并显示在 `ufw status` 中。
  * **使用服务名：** 优先使用服务名称 (`ssh`, `http`, `https`) 而非端口号 (`22`, `80`, `443`)，因为它更具可读性。UFW 会自动解析 `/etc/services` 文件中的名称。
  * **IPv6：** UFW 默认会同时为 IPv4 和 IPv6 配置规则。如果你不需要 IPv6，可以在 `/etc/default/ufw` 中禁用它。

-----

## 📚 信息参考

  * **UFW 官方文档**
  * **`man ufw`**：在命令行中查看 UFW 的完整手册页。
  * **`/etc/default/ufw`**：主要的 UFW 配置文件，用于设置默认策略、IPv6 支持等。
  * **`/etc/ufw/user.rules`**：UFW 实际存储用户添加的规则的地方。


