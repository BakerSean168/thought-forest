---
tags: [status/growing, type/resource, type/concept]
description: Quartz-for-Obsidian
created: 2026-01-12T20:21:01
updated: 2026-01-12T20:22:22
---

# Quartz-for-Obsidian 


## 概览

简单来说，它是一个网站生成器。

它的工作原理是：读取你的 Obsidian 笔记文件夹（Markdown 文件） -> 自动转换成漂亮的网页（HTML/JS/CSS） -> 发布到网上。

它是由开发者 Jacky Zhao 开发的开源项目，核心目的就是为了让普通人也能拥有像“Obsidian Publish”那样丝滑的发布体验，但是**完全免费**且**高度可定制**。

### 2. 为什么说它是 Obsidian 的“绝配”？

Quartz 最大的卖点就是**“原生感”**，它极力还原了 Obsidian 的核心功能：

- **双向链接 (Wikilinks)：** 支持 `[[笔记名]]` 这种链接，点击就能跳转，不需要改成标准的 `[链接](url)` 格式。
- **关系图谱 (Graph View)：** 网页右上角或底部会自动生成一个互动的关系图谱，展示当前笔记和其他笔记的关联。
- **悬浮预览 (Popovers)：** 鼠标悬停在链接上时，会像 Obsidian 一样弹出一个小窗口预览内容。
- **反向链接 (Backlinks)：** 页面底部会自动列出“有哪些其他笔记引用了当前这篇笔记”。
- **Callouts (警告/提示块)：** 支持 `> [!info]` 这种漂亮的提示框。
- **极速加载 (SPA)：** 它是单页应用，点击链接切换页面时不会白屏刷新，体验极其流畅。
- **支持 Latex 公式与 Mermaid 图表：** 理工科笔记也能完美展示。

### 3. 如何把你的“工具箱”想法融入进去？

这就是 Quartz v4 相比于 v3 (Hugo版) 的最大优势：**它基于 TypeScript 和 JSX (React)**。

如果你想在网站里加一个“汇率换算器”或者“简历下载按钮”：

1. **不懂代码：** 它可以直接识别 Markdown 里的 HTML 标签，你可以直接嵌入 `<iframe>`。
2. **懂一点代码：** 你可以写一个简单的 React 组件（比如 `MyTool.tsx`），然后在配置文件里注册一下，就可以在任意 Markdown 页面里像写插件一样调用它。

## 上手指南：如何搭建？

不需要太复杂的编程知识，只要会用终端（命令行）敲几行字即可。

**准备工作：**

- 安装 **Node.js** (版本 18.14 以上)
- 有一个 **GitHub** 账号
- 安装 **Git**

**步骤演示：**

1. 下载模板：
	
	打开终端（CMD 或 Terminal），输入以下命令把 Quartz 下载下来：

	```
    npm create quartz@latest
    ```

	_(它会问你几个问题，比如要不要把名字改成你的名字，一路回车选 Yes 即可)_
	
2. **进入目录并安装依赖：**
	
	```
    cd quartz
    npm install
    ```

3. **本地预览：**
	
	```
    npx quartz build --serve
    ```

	这时打开浏览器访问 `http://localhost:8080`，你就能看到一个默认的精美网站了！
	
4. **连接你的 Obsidian：**
	
	- 在你的电脑里找到 `quartz/content` 这个文件夹。
	- 把这里面的文件删掉，替换成你 Obsidian 里的笔记（或者用软件把你的 Obsidian 仓库直接同步到这个文件夹）。
	- 再次刷新网页，你的笔记就变网站了。
		
5. **发布上线 (推荐 Vercel)：**
	
	- 把这个 quartz 文件夹上传到你的 GitHub。
	- 去 **Vercel** 官网，用 GitHub 登录，点击 "Add New Project"，选中你的 quartz 仓库。
	- 点击 "Deploy"。
	- 以后你只需要在本地写笔记 -> 推送到 GitHub，Vercel 就会自动帮你更新网站。

### 5. 常见误区提醒

- **不要全部发布：** 你的 Obsidian 库里肯定有私密日记。Quartz 支持你在笔记开头写 `published: false` 来隐藏某个文件，或者你可以在配置里设置只发布带有 `#public` 标签的笔记。
- **图片问题：** 记得把图片附件放在 Quartz 能读取到的文件夹里（通常建议放在 `content` 目录下）。
