---
tags:
  - tech/app/obsidian
  - tech/devops/deploy
  - type/howto
  - status/evergreen
description: Quartz 4.0 静态网站生成与部署完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[同步与部署]]

---

# Quartz 4.0 静态部署指南

> **核心功能**：将 Obsidian 笔记转换为静态网站、搜索、自定义主题  
> **难度级别**：⭐⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：高端用户/博主必用

---

## 📋 快速导航

- [什么是 Quartz](#什么是-quartz)
- [核心功能](#核心功能)
- [环境准备](#环境准备)
- [基础部署](#基础部署)
- [自定义配置](#自定义配置)
- [主题与美化](#主题与美化)
- [部署到云端](#部署到云端)
- [进阶技巧](#进阶技巧)
- [常见问题](#常见问题)

---

## 什么是 Quartz

**定义**：
Quartz 是一个快速、简洁、超可定制的 Obsidian 静态网站生成器（SSG）。

**对比其他方案**

| 方案 | 速度 | 易用度 | 自定义 | 推荐场景 |
|-----|------|--------|--------|---------|
| **Quartz 4.0** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 知识库、博客、文档 |
| **Obsidian Publish**（官方） | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | 快速分享，不需要自定义 |
| **Digital Garden** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | 简单发布，个人笔记 |
| **Hugo** | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | 博客、复杂自定义 |
| **Next.js** | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ | 高度定制，学习成本高 |

---

## 核心功能

### 🎯 主要特性

| 功能 | 说明 |
|-----|------|
| **一键部署** | GitHub Pages/Netlify 自动部署 |
| **全文搜索** | 浏览器端搜索（不需要服务器） |
| **响应式设计** | 自动适配手机、平板、桌面 |
| **暗黑模式** | 原生支持 Light/Dark 主题切换 |
| **链接预览** | 悬浮预览 Markdown 链接内容 |
| **反向链接** | 显示哪些文件链接到当前页 |
| **目录导航** | 自动生成侧边栏与大纲 |
| **数学公式** | LaTeX 公式渲染支持 |
| **代码高亮** | 语法高亮与主题定制 |
| **SEO 优化** | Meta tags、Sitemap、Open Graph |

### 📊 配置对比

**简单配置**（5 分钟，适合博客）
```
✅ 启用搜索
✅ 设置站点标题
✅ 选择主题颜色
✅ 部署到 GitHub Pages
```

**中等配置**（30 分钟，适合知识库）
```
✅ 自定义导航菜单
✅ 配置分类标签系统
✅ 修改样式与颜色
✅ 设置 Google Analytics
✅ 添加自定义组件
```

**高级配置**（2+ 小时，适合企业）
```
✅ 修改 Hugo 模板
✅ 自定义首页设计
✅ 集成第三方服务（评论系统等）
✅ 性能优化与 CDN 配置
```

---

## 环境准备

### 前置条件检查

```bash
# 检查 Node.js（v18+）
node --version    # 应显示 v18.x 或更高

# 检查 npm
npm --version

# 检查 Git
git --version
```

### 安装必需工具

**Windows**

```powershell
# 使用 winget 安装（推荐）
winget install nodejs
winget install git

# 或手动下载
# Node.js: https://nodejs.org/
# Git: https://git-scm.com/
```

**Mac**

```bash
# 使用 Homebrew
brew install node
brew install git
```

**Linux**

```bash
# Ubuntu/Debian
sudo apt-get install nodejs npm git

# Fedora
sudo dnf install nodejs npm git
```

### 获取 Quartz

**方式 1：使用模板（推荐新手）**

1. 访问 https://github.com/jackyzha0/quartz
2. 点击 "Use this template"
3. 创建新仓库，名称如 `obsidian-vault-site`
4. 克隆到本地

```bash
git clone https://github.com/YOUR_USERNAME/obsidian-vault-site.git
cd obsidian-vault-site
```

**方式 2：从源码构建**

```bash
git clone https://github.com/jackyzha0/quartz.git
cd quartz
npm install
npm run build
```

---

## 基础部署

### 步骤 1：配置项目

**修改配置文件** `quartz.config.ts`

```typescript
import { QuartzConfig } from "./quartz/cfg"
import * as Plugin from "./quartz/plugins"

const config: QuartzConfig = {
  configuration: {
    // 🔧 基本设置
    pageTitle: "我的知识库",                    // 网站标题
    enableSPA: true,                          // 启用单页应用
    enablePopovers: true,                     // 启用链接悬浮预览
    enableFooter: true,                       // 显示页脚
    locale: "zh-CN",                          // 语言（中文）
    
    // 🎨 样式设置
    baseUrl: "example.com",                   // 你的域名
    ignorePatterns: ["private", "templates"], // 忽略文件夹
    defaultDateType: "created",               // 默认使用创建时间
  },
  
  // 📝 插件配置
  plugins: {
    transformers: [
      Plugin.FrontMatter(),
      Plugin.CreatedModifiedDate(),
      Plugin.Latex({ renderEngine: "katex" }),
      Plugin.SyntaxHighlighting(),
      Plugin.GitHubFlavoredMarkdown(),
      Plugin.TableOfContents(),
      Plugin.CrawlLinks({ markdownLinkResolution: "shortest" }),
    ],
    filters: [Plugin.RemoveDrafts()],
    emitters: [
      Plugin.AliasRedirects(),
      Plugin.ComponentResources({ fontsize: "16px", imgResizeService: "imgix" }),
      Plugin.ContentPage(),
      Plugin.FolderPage(),
      Plugin.TagPage(),
      Plugin.ContentIndex({ enableSiteMap: true, enableRSS: true }),
      Plugin.Assets(),
      Plugin.Static(),
      Plugin.NotFoundPage(),
    ],
  },
}

export default config
```

### 步骤 2：复制笔记文件

```bash
# 复制 Obsidian 库到 Quartz 内容目录
cp -r /path/to/obsidian/vault ./content

# 或符号链接（推荐，实时同步）
ln -s /path/to/obsidian/vault ./content
```

### 步骤 3：本地预览

```bash
npm run dev
```

**输出**：
```
✓ Built successfully in 2.34s

[15:30:42] Starting development server
[15:30:42] Listening on http://localhost:3000
```

打开浏览器访问 `http://localhost:3000` 预览效果。

### 步骤 4：构建静态文件

```bash
npm run build
```

**输出**：
```
dist/
├── index.html
├── search/
├── static/
└── ... (所有转换后的 HTML 文件)
```

---

## 自定义配置

### 配置导航菜单

**修改 `quartz.config.ts`**

```typescript
configuration: {
  // ... 其他配置
  
  // 导航栏菜单
  navigation: [
    {
      title: "首页",
      links: [
        { name: "关于我", path: "about" },
        { name: "使用指南", path: "guide" },
      ],
    },
    {
      title: "知识库",
      links: [
        { name: "计算机科学", path: "CS" },
        { name: "学习笔记", path: "learning" },
      ],
    },
  ],
}
```

### 配置标签系统

**在笔记中添加 frontmatter**

```yaml
---
title: "我的第一篇笔记"
tags:
  - javascript
  - web-dev
  - tutorial
---

内容...
```

**自动生成标签页面**：
- `/tags/` 页面自动生成所有标签列表
- `/tags/javascript/` 显示标记为 `javascript` 的所有笔记

### 配置样式与颜色

**创建自定义 CSS**（`quartz/styles/custom.scss`）

```scss
// 主题颜色
$primary-color: #5b8def;    // 蓝色
$secondary-color: #e84d4d;  // 红色
$background-color: #f5f5f5;

// 字体
$font-family: "Noto Sans CJK SC", -apple-system, BlinkMacSystemFont, sans-serif;
$font-size: 16px;
$line-height: 1.7;

// 链接颜色
a {
  color: $primary-color;
  text-decoration: none;
  
  &:hover {
    text-decoration: underline;
  }
}

// 代码块样式
code {
  background-color: rgba(0, 0, 0, 0.05);
  padding: 2px 6px;
  border-radius: 3px;
}

pre code {
  background-color: transparent;
  padding: 0;
}
```

### 配置 SEO

```typescript
configuration: {
  description: "一个关于编程和学习的知识库",
  author: "Your Name",
  
  // Open Graph 社交分享
  ogImage: "https://example.com/og-image.png",
  ogType: "website",
}
```

---

## 主题与美化

### 使用预设主题

**Quartz 自带主题**

| 主题 | 特点 |
|-----|------|
| **light** | 明亮，适合白天阅读 |
| **dark** | 深色，护眼 |
| **auto** | 根据系统偏好自动切换 |

**切换主题**

```typescript
// quartz.config.ts
configuration: {
  theme: {
    typography: {
      header: "Schibsted Grotesk",
      body: "Source Sans Pro",
      code: "IBM Plex Mono",
    },
    colors: {
      lightMode: {
        light: "#faf8f3",
        lightgray: "#e5e5e5",
        gray: "#b8b8b8",
        darkgray: "#4e4e4e",
        dark: "#2b2b2b",
        success: "#4d9221",
        base: "#fffaf3",
        basalt: "#4e4e4e",
      },
      darkMode: {
        light: "#161618",
        lightgray: "#393639",
        gray: "#646464",
        darkgray: "#d4d4d4",
        dark: "#ebebec",
        success: "#80e130",
        base: "#161618",
        basalt: "#e8e8e8",
      },
    },
  },
}
```

### 自定义主页

**创建 `content/index.md`**

```markdown
---
title: "欢迎来到我的知识库"
---

# 👋 欢迎

这是我的个人知识库，记录了我在编程、设计、生活中的所有笔记与思考。

## 🚀 快速导航

- [[编程笔记|CS]] - 计算机科学相关
- [[学习资源|Resources]] - 优质学习资源
- [[生活思考|Life]] - 个人成长与反思

## 📊 统计

- 总笔记数：{{wordCount}} 字
- 最近更新：{{lastModified}}

## 🔥 热门文章

[[我最重要的笔记]]
[[高频阅读的内容]]

---

更多内容见 [[导航|Navigation]]
```

---

## 部署到云端

### 方式 1：GitHub Pages（免费，推荐）

**优点**：
- 完全免费
- 自动部署
- 自动 HTTPS
- 无服务器成本

**配置步骤**

1. **推送代码到 GitHub**

```bash
git add .
git commit -m "Initial Quartz setup"
git push -u origin main
```

2. **启用 GitHub Pages**

在 GitHub 仓库：
```
Settings → Pages
→ Build and deployment
→ Source: GitHub Actions
→ Deploy from branch: main
```

3. **创建工作流文件** `.github/workflows/build.yml`

```yaml
name: Build and Deploy Quartz

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
      
      - name: Install dependencies
        run: npm install
      
      - name: Build Quartz
        run: npm run build
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

4. **验证部署**

访问 `https://YOUR_USERNAME.github.io/obsidian-vault-site`

### 方式 2：Netlify（免费 + 高级选项）

**优点**：
- 免费 SSL
- 自定义域名
- 构建预览
- 边缘函数支持

**配置步骤**

1. 访问 https://app.netlify.com/
2. 选择"Connect from Git"
3. 连接 GitHub 账户，授权
4. 选择 Quartz 仓库
5. **构建设置**：
   ```
   Build command: npm run build
   Publish directory: dist
   ```
6. 点击"Deploy"

**自定义域名**：
```
Domain settings → Add custom domain
→ 配置 DNS（根据 Netlify 指引）
```

### 方式 3：Vercel（推荐高端用户）

**优点**：
- 极快的全球 CDN
- 边缘计算支持
- 自动优化
- 部署分析

**配置步骤**

1. 访问 https://vercel.com
2. "Import Project" → 选择 GitHub 仓库
3. **框架**：Select "Other"
4. **构建命令**：`npm run build`
5. **输出目录**：`dist`
6. 点击"Deploy"

**结果**：
- 自动获得子域名：`YOUR_PROJECT.vercel.app`
- 支持自定义域名（付费）

---

## 进阶技巧

### 自动同步 Obsidian 笔记

**方式 1：符号链接（推荐）**

```bash
# Windows PowerShell
New-Item -ItemType SymbolicLink -Path "./content" -Target "C:\Users\YourName\Documents\Obsidian Vault"

# Mac/Linux
ln -s ~/Documents/"Obsidian Vault" ./content
```

**优点**：
- 笔记和网站实时同步
- 修改立即生效
- 节省存储空间

### 集成评论系统

**使用 Giscus（GitHub 讨论）**

```typescript
// quartz.config.ts
plugins: {
  emitters: [
    // ... 其他 emitters
    Plugin.Giscus({
      repo: "YOUR_USERNAME/obsidian-vault-site",
      repoId: "YOUR_REPO_ID",
      category: "Announcements",
      categoryId: "DIC_kwDOAxx...",
      mapping: "pathname",
      strict: false,
      reactionsEnabled: true,
      emitMetadata: false,
      inputPosition: "bottom",
      lang: "zh-CN",
      loading: "lazy",
    }),
  ],
}
```

### 集成分析

**Google Analytics**

```typescript
plugins: {
  emitters: [
    Plugin.GoogleAnalytics({
      tagId: "G-XXXXXXX", // 你的 GA 测量 ID
    }),
  ],
}
```

**获取 GA ID**：
1. 访问 https://analytics.google.com
2. 创建属性，获取"测量 ID"（G-XXXXXXX）
3. 配置到上述代码

### 增加搜索功能

Quartz 已内置全文搜索，无需额外配置：

```typescript
plugins: {
  emitters: [
    Plugin.ContentIndex({
      enableSiteMap: true,  // 生成 sitemap.xml
      enableRSS: true,      // 生成 RSS 订阅
    }),
  ],
}
```

---

## 常见问题

### Q1：修改笔记后网站没有更新？

**原因**：静态网站需要重新构建

**解决方案**：

1. **本地开发**：自动刷新
   ```bash
   npm run dev
   # 修改文件后浏览器自动刷新
   ```

2. **GitHub 部署**：自动重新部署
   ```bash
   git add .
   git commit -m "Update notes"
   git push  # 自动触发 GitHub Actions
   # 等待 2-3 分钟，网站自动更新
   ```

### Q2：中文文件名导致 404？

**原因**：URL 编码问题

**解决方案**：

```typescript
// quartz.config.ts
plugins: {
  transformers: [
    Plugin.CrawlLinks({
      markdownLinkResolution: "shortest",
      // 使用相对路径而非文件名
    }),
  ],
}
```

或改用英文文件名：
```
❌ 我的笔记.md
✅ my-notes.md

// 使用 title 在 frontmatter 中定义中文标题
---
title: "我的笔记"
---
```

### Q3：如何隐藏某些笔记不发布？

**方式 1：修改 frontmatter**

```yaml
---
title: "私密笔记"
draft: true  # 标记为草稿
---
```

**方式 2：使用文件夹规则**

```typescript
// quartz.config.ts
configuration: {
  ignorePatterns: [
    "private",      // 忽略 private 文件夹
    "draft",        // 忽略 draft 文件夹
    "*.tmp",        // 忽略临时文件
  ],
}
```

### Q4：网站加载很慢？

**优化方案**：

1. **启用图片优化**
   ```typescript
   Plugin.Assets({
     imageResizeService: "imgix",  // 或 "cloudinary"
   })
   ```

2. **启用 CDN**
   ```typescript
   configuration: {
     baseUrl: "https://cdn.example.com/",
   }
   ```

3. **压缩静态资源**
   ```bash
   npm install -D webpack-bundle-analyzer
   npm run analyze
   ```

4. **使用 Vercel 或 Netlify**
   - 自动 CDN 加速
   - 自动资源优化

### Q5：如何自定义域名？

**步骤**：

1. **购买域名**（Namecheap、GoDaddy 等）

2. **配置 DNS**

   **GitHub Pages**：
   ```
   CNAME 记录：
   Name: www
   Value: YOUR_USERNAME.github.io
   
   A 记录（IPv4）：
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```

3. **在仓库添加 CNAME 文件**

   创建 `public/CNAME`：
   ```
   example.com
   ```

4. **更新 quartz.config.ts**

   ```typescript
   configuration: {
     baseUrl: "example.com",
   }
   ```

---

## 部署检查清单

- [ ] 配置 `quartz.config.ts`
- [ ] 复制笔记到 `content/` 目录
- [ ] 本地预览 `npm run dev` 无误
- [ ] 推送到 GitHub
- [ ] 启用 GitHub Pages
- [ ] 访问网站地址验证
- [ ] 配置自定义域名（可选）
- [ ] 配置 SEO 信息
- [ ] 配置分析跟踪（可选）
- [ ] 配置评论系统（可选）

---

## 📚 相关文档

- [[Obsidian Digital Garden插件详细指南]] - 另一种简化部署方案
- [[Obsidian Git插件详细指南]] - 版本控制与同步
- [[Obsidian同步与部署指南]] - 整合方案与工作流

---

**上级索引**：[[Obsidian插件完全索引]] | [[同步与部署]]
