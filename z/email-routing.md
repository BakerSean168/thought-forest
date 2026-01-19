---
tags:
  - status/growing
  - type/concept
description: Email Routing 是我在 Cloudflare 中发现的一个功能。可以用来实现一些邮箱上的功能。比如特殊的邮箱名字 admin@xxx.xx
created: 2026-01-19T13:46:55
updated: 2026-01-19T14:08:29
---

# Email Routing

## Overview

**Cloudflare Email Routing** 是一个完全免费的邮件转发服务。
* **作用**：它让你能够使用自定义域名（如 `xxx@bakersean.top`）接收邮件，并在后台自动转发到你真实的私人邮箱（如 Gmail）。
* **本质**：它只是一个**中转站**。它不存储邮件，只负责分发。
* **优势**：零成本、零服务器维护、无限别名、保护真实隐私。

---

## 2. 核心逻辑：优先级规则 (The Golden Rule)

Cloudflare 的处理逻辑有严格的先后顺序，理解这一点是玩转分流的关键：

> **Custom Addresses (VIP通道) > Catch-all Address (兜底通道)**

1.  **第一优先级：精确匹配 (Custom Address)**
	* 当一封信进来，系统先查“白名单”。
	* 如果有明确定义的规则（例如 `spam@bakersean.top` -> `Drop`），则**立刻执行**，不再往下走。
2.  **第二优先级：模糊匹配 (Catch-all Address)**
	* 如果这封信的收件人**不在**自定义列表里（是陌生前缀），则触发 Catch-all 规则。
	* 通常用于“接管所有”流量，实现无限别名。

---

## 实战配置手册

[[cloudflare加域名实现独特邮箱名称]]

### 阶段一：基础收信 (开启服务)

1.  **入口**：Cloudflare 后台 -> 域名 -> **Email** -> **Email Routing**。
2.  **绑定目标**：在 **Destination addresses** 添加你的真实邮箱 (推荐 Gmail/Outlook，慎用 QQ/163)，并去邮箱点击验证链接。
3.  **自动配置 DNS**：点击 "Add records automatically"，系统会自动添加 MX 和 SPF 记录。

### 阶段二：开启无限模式 (Catch-all)

* **位置**：**Routing rules** 标签页 -> 下半部分 **Catch-all address**。
* **操作**：点击 Edit -> Action 选 `Send to an email` -> 目标选你的真实邮箱 -> 状态改为 `Active`。
* **效果**：现在你拥有了无限个邮箱。
	* 注册京东填 `jd@bakersean.top` -> 能收到。
	* 注册淘宝填 `taobao@bakersean.top` -> 能收到。
	* 随便编一个 `hello@bakersean.top` -> 也能收到。

### 阶段三：高级分流策略 (Custom Rules)

在 **Routing rules** -> **Custom addresses** 中创建规则，实现精细化管理：

#### 场景 A：垃圾黑洞 (Drop)

* **需求**：之前注册的小网站泄露了你的邮箱 `game@bakersean.top`，天天收广告。
* **配置**：
	* **Custom address**: `game`
	* **Action**: `Drop` (直接丢弃)
* **结果**：这个特定前缀的邮件直接消失，世界清净了。其他邮件依然走 Catch-all 正常接收。

#### 场景 B：家庭/团队分发 (Forwarding)

* **需求**：给家人开通专属邮箱。
* **配置**：
	* **Custom address**: `wife`
	* **Destination**: `wife-real-email@qq.com` (填写家人的真实邮箱)
* **结果**：发给老婆的信直接转给她，不会发给你。

#### 场景 C：多身份隔离

* **需求**：把工作和生活分开。
* **配置**：
	* `work@...` -> 转发给公司 Outlook 邮箱。
	* `*@...` (Catch-all) -> 转发给私人 Gmail。

---

## 4. 常见问题与坑 (FAQ)

### Q1: 能用这个域名发邮件吗？

* **默认不行**。Email Routing 只管收，不管发。
* **解决方案**：使用 **Gmail SMTP** 方案。在 Gmail 设置中添加“发送邮件为”，填入你的域名邮箱，SMTP 服务器填 `smtp.gmail.com`，使用 Google 应用专用密码认证。这样就能以 `admin@bakersean.top` 的身份回信了。

### Q2: 为什么不推荐用国内邮箱 (QQ/163) 作为目标？

* **拒信风险**：Cloudflare 转发的邮件，发件人 IP 和域名不匹配（代转发）。国内邮箱的反垃圾策略极其严格，经常会直接拒收或退信，导致你收不到验证码。
* **推荐**：Gmail > Outlook > iCloud >>> 国内邮箱。

### Q3: Catch-all 收到太多垃圾邮件怎么办？

* 如果在 Catch-all 模式下，某个特定的前缀（比如 `contact@`）被黑客扫到了，疯狂发垃圾。
* **解法**：添加一条 **Custom Address**，把这个特定的 `contact` 前缀设置为 **Drop**。
* **原理**：利用“Custom 优先级高于 Catch-all”的规则，定点清除污染源。

---

## 5. 命名灵感库

* **最高权限**：`root`, `admin`, `hostmaster`
* **极客范**：`sudo`, `dev`, `404`, `bot`, `no-reply`
* **个人**：`i`, `me`, `hi`, `contact`
* **专用**：`auth` (仅用于两步验证/密码库), `social` (社交网络), `shopping` (购物)