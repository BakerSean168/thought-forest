---
tags: [type/howto, status/evergreen]
description: 如何xxx
created: 2026-01-19T13:38:37
updated: 2026-01-19T13:39:07
---
[[email-routing]]
# cloudflare加域名实现独特邮箱名称 

## 1. 核心原理

利用 Cloudflare 免费提供的 **Email Routing (邮件路由)** 功能。
- **收信**：别人发给 `xxx@你的域名` -> Cloudflare 接收 -> 自动转发到你的真实邮箱（Gmail/QQ/Outlook）。
- **发信**：默认只能收。如需以域名邮箱发信，需配置 Gmail SMTP 或第三方中继（如 Brevo）。

## 2. 准备工作

- [ ] 一个托管在 Cloudflare 的域名 (例如 `xiaowang.com`)
- [ ] 一个常用的真实邮箱 (作为最终收件箱，推荐国外邮箱，国内可能直接当垃圾邮件)

## 3. 配置步骤 (实现收信)

### 第一步：开启服务

1. 登录 Cloudflare 后台 -> 选择域名。
2. 左侧菜单点击 **Email** -> **Email Routing**。
3. 点击 **Get Started**。

### 第二步：绑定目标邮箱

1. 在 **Destination addresses** 中输入你的真实邮箱（如 `yourname@gmail.com`）。
2. **关键**：去你的真实邮箱查收验证邮件，点击链接验证。
3. 回到 Cloudflare，状态应显示为 `Verified`。

### 第三步：创建别名 (Custom Address)

在 **Routing rules** 选项卡下，点击 **Create address**：
- **Custom address**: 输入你想要的前缀 (例如 `admin`)。
- **Destination**: 选择刚才验证的真实邮箱。
- **Action**: 保持 `Send to`。
- 点击 **Save**。

### 第四步：设置 DNS 记录

- Cloudflare 会提示 "Missing DNS records"。
- 直接点击 **Add records automatically** (自动添加)。
- 它会自动添加 MX 记录和 SPF 记录，无需手动操作。

### 第五步：开启 Catch-all (接管所有) —— **强烈推荐**

不想每次都手动创建别名？
1. 进入 **Settings** 选项卡。
2. 找到 **Catch-all address**。
3. 点击 **Edit** -> Action 选 `Send to` -> 目标选你的真实邮箱 -> 状态改为 **Active**。
- **效果**：只要后缀是 `@你的域名`，不管前缀是你编的什么 (如 `taobao@...`, `abc@...`)，全部都能收到！

---

## 4. 进阶：如何用域名邮箱发信 (SMTP)

Cloudflare 邮件路由只管收，不管发。要实现“伪装”发信，最稳妥的方案是 **Gmail SMTP**。

1. **申请 Google App Password (应用专用密码)**：
   - Google 账号 -> 安全性 -> 两步验证 -> 应用专用密码 -> 生成一个 (记下来)。
2. **在 Gmail 中添加账号**：
   - Gmail 设置 -> 账号和导入 -> **发送邮件为** -> **添加另一个电子邮件地址**。
   - **名称**：填你想显示的名字 (如 `Baker Sean`)。
   - **电子邮件地址**：填 `admin@你的域名`。
   - **保持勾选** "作为别名处理" [[作为别名treat-as-alias-IN-email-routing]]。
1. **SMTP 设置**：
   - **SMTP 服务器**: `smtp.gmail.com`
   - **端口**: `587` (TLS) 或 `465` (SSL)
   - **用户名**: `你的真实Gmail地址` (不是域名邮箱!)
   - **密码**: 刚才生成的 **Google 应用专用密码**。
4. **验证**：
   - Gmail 会发一封验证邮件给 `admin@你的域名`。
   - Cloudflare 会自动转发回你的 Gmail。
   - 点开邮件，获取验证码填入即可。

---

## 5. 命名灵感 (Naming Ideas)

### 极客/技术流

- `root@...` (系统最高权限)
- `admin@...` (管理员，最标准)
- `dev@...` (开发者)
- `bot@...` (机器人/自动通知)
- `404@...` (找不到人)
- `sudo@...` (听令)

### 极简/个人

- `i@...`
- `me@...`
- `hi@...`
- `yo@...`
- `contact@...`

### 功能隔离 (用于注册账号)

- `auth@...` / `security@...` (**专用**：只用于 Vaultwarden、银行、支付宝等高价值账号)
- `social@...` (注册推特、知乎)
- `spam@...` / `trash@...` (注册不靠谱的小网站)
- `shopping@...` (购物专用)

---

## 6. 安全提示

> [!WARNING] 注意
> 
>1. **隐私保护**：利用 Catch-all 功能，给不同网站注册时使用不同前缀（如 `jd@...`），收到垃圾邮件时一眼就能看出是谁泄露了你的信息。