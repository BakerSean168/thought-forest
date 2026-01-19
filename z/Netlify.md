---
tags:
  - status/growing
  - type/resource
  - tech/ops/deploy
description: Netlify 是现代 web 开发（特别是静态网站）的**“托管神器”**。
created: 2026-01-13T20:54:09
updated: 2026-01-13T20:55:03
---

# Netlify 

### 1. Netlify 到底是做什么的？

它是一个**自动化托管平台**。它最核心的工作流叫做 **Git-based Workflow**：

1. 你在本地写好代码（或笔记），推送到 **GitHub**。
2. Netlify 监听到 GitHub 有变动，自动把代码拉下来。
3. Netlify 在它的云端服务器上运行构建命令（比如 `npm run build`）。
4. 构建生成的 HTML/CSS/JS 文件，瞬间被分发到它全球的 **CDN 节点**（让全世界访问都很快）。
5. 你的网站更新了。

**全程你不需要买服务器、不需要配置 Nginx、不需要用 FTP 上传文件。** 你只需要 `git push`。

---

### 2. 为什么它适合你的“Obsidian 知识库”或“工具箱”？

对于你的需求，Netlify 有几个非常显著的优势：

#### A. 极其慷慨的免费额度 (Free Tier)

对于个人博客、简历、小型工具箱，Netlify 的免费版几乎是用不完的：

- **带宽：** 每月 100GB（哪怕你每天有几千人访问都够了）。
- **构建时间：** 每月 300 分钟（每次构建大概 1-2 分钟，够你每天更新很多次）。
- **HTTPS：** 自动赠送并管理 SSL 证书（你的网站直接就有小绿锁）。

#### B. 全球 CDN 加速

你之前提到过 VPS 和网络问题。如果你用传统的 VPS（比如阿里云、Azure）架设网站，服务器在美国，国内访问就慢。

Netlify 是把你的静态文件缓存在全球各地的节点上。用户访问时，会自动从离他最近的节点获取数据。

#### C. Serverless Functions（无服务器函数）—— 做“工具箱”的神器

这与你的“工具箱”需求高度相关。

通常静态网站（HTML）没法处理动态逻辑（比如：发邮件、从数据库读取数据、调用 OpenAI 接口）。要实现这些，以前你得买个 VPS 写后端。

在 Netlify 上，你可以写一个简单的 JavaScript 函数（放在 `netlify/functions` 目录下），Netlify 会自动把它部署成一个 API 接口。

- **场景：** 你的网站上有一个“留言板”或者“天气查询工具”。
- **实现：** 前端用 Astro/React 写界面，后端逻辑写在 Netlify Function 里。**你依然不需要管理服务器。**

#### D. 表单处理 (Netlify Forms)

你想在简历页面放一个“联系我”的表单？

你不需要写任何后端代码，只需要在 HTML 的 <form> 标签里加一个 netlify 属性。Netlify 会自动拦截提交的数据，你可以在 Netlify 的后台管理面板看到谁给你发了消息，甚至设置邮件通知。

---

### 3. Netlify vs Vercel vs GitHub Pages

这三家是目前最主流的选择，简单对比如下：

|**特性**|**Netlify**|**Vercel**|**GitHub Pages**|
|---|---|---|---|
|**主打场景**|通用静态站，工具箱，Vue/React/Hugo等|**Next.js 的亲爹**，React 生态极强|纯静态展示，文档|
|**构建速度**|快|极快|较慢|
|**国内访问**|尚可 (有时候 DNS 会被干扰)|**目前国内访问体验最好**|经常抽风/无法访问|
|**Serverless**|支持很好 (AWS Lambda 封装)|支持极好|不支持 (纯静态)|
|**上手难度**|⭐ (傻瓜式)|⭐ (傻瓜式)|⭐⭐ (需配置 Action)|

**我的建议：**

- 如果你选 **Astro** 或 **Quartz**：**Netlify** 和 **Vercel** 都是顶级选择。
- **国内访问考量：** 目前 **Vercel** 在国内的访问速度和稳定性稍微优于 Netlify（Netlify 的主域名 `netlify.app` 偶尔会在某些地区被运营商干扰，绑定自定义域名后会好很多）。

---

### 4. 实际操作：如何把你的网站部署到 Netlify？

假设你已经用 Astro 或 Quartz 写好了代码，并传到了 GitHub。

1. 注册 Netlify (直接用 GitHub 账号登录)。
2. 点击 **"Add new site"** -> **"Import from an existing project"**。
3. 选择 **GitHub**。
4. 搜索并选中你的那个仓库。
5. **关键步骤：配置构建命令**。
	
	- Netlify 通常极其聪明，它会识别出你用的是 Astro 还是 Quartz，自动帮你填好。
	- 比如 Astro，它会自动填 `npm run build`，发布目录填 `dist`。
		
6. 点击 **"Deploy Site"**。

等待几十秒，它会给你一个随机域名的网址（例如 `happy-beaver-123456.netlify.app`），你的网站就上线了！

### 总结

- **Netlify** 是你发布网站的**服务器管家**。
- 它**免费、自动、且支持后端函数**。
- 如果你要做一个带有**动态工具**（API调用、表单）的个人网站，它是比 GitHub Pages 更强大的选择。

下一步：

你是打算先试着用 Netlify 部署一个简单的 Demo，还是想先回去研究 Quartz/Astro 的代码？