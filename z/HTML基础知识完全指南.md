---
tags:
  - tech/lang/html
  - type/howto
  - status/evergreen
description: HTML 基础知识完全指南，包含所有常用标签、属性、语义化等
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[HTML]] | [[计算机语言 MOC]] | [[前端开发 MOC]]

---

# HTML 基础知识完全指南

> HTML (HyperText Markup Language) 是 Web 的基础，用于定义网页的结构和内容。  
> 本文档涵盖所有常用标签、属性、最佳实践。

---

## 📚 HTML 历史与版本

| 版本 | 发布时间 | 特点 |
|------|---------|------|
| HTML 4.01 | 1999 | 经典版本，已过时 |
| XHTML 1.0 | 2000 | XML 严格语法，已过时 |
| HTML5 | 2014 | 现代标准，推荐使用 ✅ |

---

## 🏗️ HTML 基本结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>页面标题</title>
  </head>
  <body>
    <h1>欢迎</h1>
  </body>
</html>
```

### 关键标签说明

| 标签 | 说明 | 必需 |
|------|------|------|
| `<!DOCTYPE html>` | HTML5 文档声明 | ✅ |
| `<html>` | 根元素 | ✅ |
| `<head>` | 页面头部（元数据） | ✅ |
| `<body>` | 页面主体（可见内容） | ✅ |

---

## 📋 HEAD 标签详解

> `<head>` 包含的是元数据，不会直接显示在页面上

### 常用 head 标签

```html
<head>
  <!-- 字符集 -->
  <meta charset="UTF-8">
  
  <!-- 视口设置（响应式设计必需） -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  
  <!-- SEO 相关 -->
  <meta name="description" content="页面描述">
  <meta name="keywords" content="关键词1, 关键词2">
  <meta name="author" content="作者名">
  
  <!-- 页面标题 -->
  <title>页面标题 - 网站名</title>
  
  <!-- 链接到样式表 -->
  <link rel="stylesheet" href="style.css">
  <link rel="icon" href="favicon.ico">
  
  <!-- 内联样式（不推荐） -->
  <style>
    body { color: black; }
  </style>
  
  <!-- 内联脚本 -->
  <script>
    console.log('Hello');
  </script>
</head>
```

### Meta 标签详解

```html
<!-- 字符编码（必需） -->
<meta charset="UTF-8">

<!-- 视口（响应式必需） -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- SEO -->
<meta name="description" content="200 字以内的页面描述">
<meta name="keywords" content="关键词">

<!-- 网页刷新重定向（不推荐） -->
<meta http-equiv="refresh" content="5; url=https://example.com">

<!-- 兼容性 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- Open Graph（社交媒体分享） -->
<meta property="og:title" content="标题">
<meta property="og:description" content="描述">
<meta property="og:image" content="image-url">
<meta property="og:url" content="page-url">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary">
<meta name="twitter:title" content="标题">
```

---

## 📝 BODY 中的文本标签

### 标题标签 (Heading)

```html
<h1>一级标题 - 每个页面只应有一个</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题 - 最小</h6>
```

**最佳实践**：
- 只有一个 `<h1>` 用于页面主标题
- 按层级使用，不要跳级（h1 → h3）

### 段落与换行

```html
<!-- 段落 -->
<p>这是一个段落。段落之间有间距。</p>
<p>另一个段落。</p>

<!-- 软换行（空格 + 换行，显示为一行） -->
<p>这是第一行<br>这是第二行</p>

<!-- 水平线 -->
<hr>

<!-- 预格式化文本（保留空格和换行） -->
<pre>
  这个    有空格
  这个有换行
</pre>
```

### 文本格式化标签

| 标签 | 说明 | 语义 | 用途 |
|------|------|------|------|
| `<b>` | 粗体 | 无 | 样式用 ❌ |
| `<strong>` | 强调粗体 | 有 | 重要内容 ✅ |
| `<i>` | 斜体 | 无 | 样式用 ❌ |
| `<em>` | 强调斜体 | 有 | 强调内容 ✅ |
| `<u>` | 下划线 | 无 | 容易混淆 ⚠️ |
| `<ins>` | 插入 | 有 | 新增内容 |
| `<del>` | 删除 | 有 | 删除内容 |
| `<mark>` | 标记/高亮 | 有 | 突出内容 |
| `<small>` | 小字体 | 有 | 免责声明 |
| `<sub>` | 下标 | 有 | H₂O |
| `<sup>` | 上标 | 有 | x² |
| `<code>` | 代码 | 有 | 代码片段 |
| `<kbd>` | 键盘输入 | 有 | Ctrl+C |
| `<samp>` | 计算机输出 | 有 | `$ output` |
| `<var>` | 变量 | 有 | 数学/编程 |

**最佳实践**：使用语义标签（有 ✅），避免样式用的标签

```html
<!-- ✅ 推荐 -->
<strong>重要</strong>
<em>强调</em>
<mark>标记</mark>

<!-- ❌ 避免 -->
<b>粗体</b>
<i>斜体</i>
```

---

## 🔗 链接标签

### `<a>` 锚点标签

```html
<!-- 基础链接 -->
<a href="https://example.com">点击这里</a>

<!-- 新标签页打开 -->
<a href="https://example.com" target="_blank">新标签页打开</a>

<!-- 链接到页面内部 -->
<a href="#section1">跳转到第一部分</a>
<h2 id="section1">第一部分</h2>

<!-- 邮件链接 -->
<a href="mailto:test@example.com">发送邮件</a>

<!-- 电话链接 -->
<a href="tel:+8613800000000">拨打电话</a>

<!-- 下载文件 -->
<a href="/download/file.pdf" download>下载 PDF</a>

<!-- 链接属性详解 -->
<a href="url"
   target="_self"           <!-- 打开方式：_self/_blank/_parent/_top -->
   rel="noopener noreferrer" <!-- 关系：nofollow/noopener/noreferrer -->
   title="鼠标悬停提示">
  链接文本
</a>
```

### target 属性

| 值 | 说明 |
|---|---|
| `_self` | 当前标签页打开（默认） |
| `_blank` | 新标签页打开 |
| `_parent` | 父框架打开 |
| `_top` | 整个浏览器窗口打开 |

### rel 属性

| 值 | 说明 |
|---|---|
| `nofollow` | 不传递权重（SEO） |
| `noopener` | 防止 window.opener 访问（安全） |
| `noreferrer` | 不发送 Referer 头 |

---

## 📸 图片标签

### `<img>` 图片

```html
<!-- 基础用法 -->
<img src="image.jpg" alt="图片描述">

<!-- 完整属性 -->
<img src="image.jpg"
     alt="图片替代文本（SEO & 无障碍）"
     title="鼠标悬停提示"
     width="200"
     height="150"
     loading="lazy"
     srcset="image-1x.jpg 1x, image-2x.jpg 2x">

<!-- 响应式图片 -->
<img srcset="small.jpg 600w, medium.jpg 1200w, large.jpg 1600w"
     sizes="(max-width: 600px) 100vw, 50vw"
     src="default.jpg"
     alt="描述">
```

**属性说明**：

| 属性 | 说明 |
|------|------|
| `src` | 图片 URL ✅ 必需 |
| `alt` | 替代文本（SEO/无障碍） ✅ 必需 |
| `width/height` | 宽度/高度（单位 px） |
| `loading` | `lazy` 延迟加载 / `eager` 立即加载 |
| `srcset` | 高清屏幕图片 |
| `sizes` | 响应式尺寸 |

### `<picture>` 响应式图片

```html
<picture>
  <source media="(max-width: 600px)" srcset="mobile.jpg">
  <source media="(max-width: 1200px)" srcset="tablet.jpg">
  <source media="(min-width: 1201px)" srcset="desktop.jpg">
  <img src="default.jpg" alt="描述">
</picture>
```

### `<figure>` 与 `<figcaption>`

```html
<!-- 图片 + 标题 -->
<figure>
  <img src="diagram.jpg" alt="流程图">
  <figcaption>图1: 系统架构图</figcaption>
</figure>
```

---

## 📋 列表标签

### 无序列表 (Unordered List)

```html
<ul>
  <li>项目 1</li>
  <li>项目 2</li>
  <li>项目 3</li>
</ul>
```

### 有序列表 (Ordered List)

```html
<!-- 数字列表 -->
<ol>
  <li>第一步</li>
  <li>第二步</li>
  <li>第三步</li>
</ol>

<!-- 自定义开始数字 -->
<ol start="10">
  <li>第十项</li>
  <li>第十一项</li>
</ol>

<!-- 自定义标号类型 -->
<ol type="A"><!-- A, a, I, i, 1(默认) -->
  <li>项目 A</li>
  <li>项目 B</li>
</ol>
```

### 描述列表 (Description List)

```html
<dl>
  <dt>HTML</dt>
  <dd>网页标记语言</dd>
  <dt>CSS</dt>
  <dd>样式表语言</dd>
</dl>
```

### 嵌套列表

```html
<ul>
  <li>前端
    <ul>
      <li>HTML</li>
      <li>CSS</li>
    </ul>
  </li>
  <li>后端</li>
</ul>
```

---

## 📊 表格标签

### 基础表格

```html
<table border="1">
  <thead>
    <tr>
      <th>姓名</th>
      <th>职位</th>
      <th>工资</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>开发</td>
      <td>15K</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>设计</td>
      <td>12K</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="2">总计</td>
      <td>27K</td>
    </tr>
  </tfoot>
</table>
```

### 表格属性

| 标签 | 说明 |
|------|------|
| `<table>` | 表格容器 |
| `<thead>` | 表头区域 |
| `<tbody>` | 表体区域 |
| `<tfoot>` | 表脚区域 |
| `<tr>` | 表行 (Table Row) |
| `<th>` | 表头单元格 |
| `<td>` | 表数据单元格 |
| `<caption>` | 表标题 |
| `<colgroup>` | 列分组 |
| `<col>` | 列定义 |

### 合并单元格

```html
<table>
  <tr>
    <td colspan="2">跨 2 列</td>
    <td>普通</td>
  </tr>
  <tr>
    <td rowspan="2">跨 2 行</td>
    <td>行 1</td>
  </tr>
  <tr>
    <td>行 2</td>
  </tr>
</table>
```

---

## 📋 表单标签

### `<form>` 表单容器

```html
<form action="/submit" method="POST" enctype="multipart/form-data">
  <!-- 表单内容 -->
  <input type="submit" value="提交">
</form>
```

### `<input>` 输入框

```html
<!-- 文本 -->
<input type="text" name="username" placeholder="用户名">

<!-- 密码 -->
<input type="password" name="password" placeholder="密码">

<!-- 邮箱 -->
<input type="email" name="email" required>

<!-- 数字 -->
<input type="number" name="age" min="0" max="150" step="1">

<!-- 范围滑块 -->
<input type="range" name="volume" min="0" max="100">

<!-- 日期 -->
<input type="date" name="birthday">
<input type="time" name="time">
<input type="datetime-local" name="datetime">

<!-- 颜色 -->
<input type="color" name="color" value="#FF0000">

<!-- 文件上传 -->
<input type="file" name="file" accept=".jpg,.png" multiple>

<!-- 复选框 -->
<input type="checkbox" name="agree" value="yes"> 同意条款

<!-- 单选框 -->
<input type="radio" name="gender" value="male"> 男
<input type="radio" name="gender" value="female"> 女

<!-- 按钮 -->
<input type="button" value="按钮">
<input type="submit" value="提交">
<input type="reset" value="重置">
<input type="hidden" name="token" value="abc123">
```

### `<textarea>` 多行文本

```html
<textarea name="comment" 
          rows="5" 
          cols="40"
          placeholder="请输入评论">
</textarea>
```

### `<select>` 下拉列表

```html
<select name="category">
  <option value="">-- 请选择 --</option>
  <option value="1">分类 1</option>
  <option value="2" selected>分类 2</option>
  <option value="3">分类 3</option>
</select>

<!-- 分组下拉列表 -->
<select name="country">
  <optgroup label="亚洲">
    <option>中国</option>
    <option>日本</option>
  </optgroup>
  <optgroup label="欧洲">
    <option>法国</option>
    <option>德国</option>
  </optgroup>
</select>
```

### `<label>` 标签关联

```html
<!-- 方式 1：使用 for 和 id -->
<label for="username">用户名</label>
<input id="username" type="text" name="username">

<!-- 方式 2：嵌套 -->
<label>
  <input type="checkbox"> 同意条款
</label>
```

### 表单完整示例

```html
<form action="/register" method="POST">
  <div>
    <label for="name">姓名</label>
    <input id="name" type="text" name="name" required>
  </div>
  
  <div>
    <label for="email">邮箱</label>
    <input id="email" type="email" name="email" required>
  </div>
  
  <div>
    <label for="message">留言</label>
    <textarea id="message" name="message" rows="5"></textarea>
  </div>
  
  <div>
    <input type="submit" value="注册">
    <input type="reset" value="重置">
  </div>
</form>
```

---

## 🎬 音视频标签

### `<audio>` 音频

```html
<!-- 基础 -->
<audio controls>
  <source src="audio.mp3" type="audio/mpeg">
  <source src="audio.ogg" type="audio/ogg">
  你的浏览器不支持音频
</audio>

<!-- 带循环 & 自动播放 -->
<audio controls autoplay loop muted>
  <source src="music.mp3" type="audio/mpeg">
</audio>
```

### `<video>` 视频

```html
<!-- 基础 -->
<video width="640" height="480" controls>
  <source src="video.mp4" type="video/mp4">
  <source src="video.webm" type="video/webm">
  你的浏览器不支持视频
</video>

<!-- 带海报、自动播放 -->
<video width="640" height="480" 
       poster="poster.jpg" 
       autoplay 
       muted 
       controls>
  <source src="video.mp4" type="video/mp4">
</video>

<!-- 字幕 -->
<video controls>
  <source src="video.mp4" type="video/mp4">
  <track kind="subtitles" src="zh.vtt" srclang="zh" label="中文">
  <track kind="subtitles" src="en.vtt" srclang="en" label="English">
</video>
```

### `<track>` 字幕

```html
<video controls>
  <source src="movie.mp4" type="video/mp4">
  <track kind="captions" src="en.vtt" srclang="en">
  <track kind="subtitles" src="zh.vtt" srclang="zh">
</video>
```

---

## 🏷️ 语义化标签

> 使用语义化标签可以提高代码可读性、SEO、无障碍访问

### 常用语义标签

```html
<!-- 页面头部 -->
<header>
  <nav>
    <a href="/">首页</a>
    <a href="/about">关于</a>
  </nav>
</header>

<!-- 主内容区 -->
<main>
  <!-- 文章 -->
  <article>
    <h1>文章标题</h1>
    <time datetime="2025-12-08">2025年12月8日</time>
    <p>文章内容...</p>
  </article>
  
  <!-- 侧边栏 -->
  <aside>
    <h3>相关链接</h3>
    <ul>
      <li><a href="#">链接 1</a></li>
    </ul>
  </aside>
</main>

<!-- 页面脚部 -->
<footer>
  <p>&copy; 2025 版权所有</p>
</footer>

<!-- 通用分组 -->
<section>
  <h2>章节标题</h2>
  <p>章节内容...</p>
</section>

<!-- 无特定语义，仅用于分组 -->
<div class="container">
  <p>内容</p>
</div>
```

### 语义标签速查

| 标签 | 说明 | 使用场景 |
|------|------|---------|
| `<header>` | 页面/章节头部 | 网站头部、文章头部 |
| `<nav>` | 导航菜单 | 主导航、面包屑 |
| `<main>` | 主内容区 | 页面主要内容 |
| `<article>` | 文章/独立内容 | 博客、新闻、评论 |
| `<section>` | 页面章节 | 按主题分组内容 |
| `<aside>` | 侧边栏/补充内容 | 侧栏、广告、相关链接 |
| `<footer>` | 页面/章节脚部 | 页脚、版权信息 |
| `<address>` | 联系信息 | 地址、邮件、电话 |

---

## 🎯 HTML 最佳实践

### 1. 文档类型声明

```html
<!-- ✅ 始终放在最前面 -->
<!DOCTYPE html>
<html lang="zh-CN">
```

### 2. 字符编码

```html
<!-- ✅ 必需，放在 head 最前面 -->
<meta charset="UTF-8">
```

### 3. 视口设置（响应式必需）

```html
<!-- ✅ 移动设备必需 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

### 4. 语义化标签

```html
<!-- ✅ 使用语义标签 -->
<header>...</header>
<main>...</main>
<footer>...</footer>

<!-- ❌ 避免滥用 div -->
<div id="header">...</div>
<div id="content">...</div>
```

### 5. 属性值引号

```html
<!-- ✅ 始终使用引号 -->
<div class="container" id="main" data-value="123">

<!-- ❌ 避免不用引号 -->
<div class=container id=main>
```

### 6. 合理使用标签

```html
<!-- ✅ 每个页面一个 h1 -->
<h1>页面主标题</h1>
<h2>子标题</h2>
<h2>另一个子标题</h2>

<!-- ❌ 避免跳级 -->
<h1>标题</h1>
<h3>直接跳到 h3</h3>
```

### 7. 可访问性 (A11y)

```html
<!-- ✅ 为 img 添加 alt -->
<img src="logo.png" alt="公司 Logo">

<!-- ✅ 为 input 关联 label -->
<label for="email">邮箱</label>
<input id="email" type="email">

<!-- ✅ 使用语义标签传递信息 -->
<button>点击</button>
<strong>重要</strong>
```

### 8. 性能优化

```html
<!-- ✅ 延迟加载图片 -->
<img src="img.jpg" loading="lazy" alt="描述">

<!-- ✅ 使用 srcset 加载合适分辨率 -->
<img srcset="small.jpg 600w, large.jpg 1200w" src="default.jpg" alt="">

<!-- ✅ 异步加载脚本 -->
<script src="app.js" async></script>
<script src="analytics.js" defer></script>
```

---

## 📱 响应式设计相关

```html
<!-- 视口设置 -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- 响应式图片 -->
<picture>
  <source media="(max-width: 600px)" srcset="mobile.jpg">
  <source media="(min-width: 601px)" srcset="desktop.jpg">
  <img src="default.jpg" alt="">
</picture>

<!-- 响应式视频（CSS 辅助） -->
<video width="100%" controls>
  <source src="video.mp4" type="video/mp4">
</video>
```

---

## 🔍 HTML 验证工具

- [W3C HTML 验证工具](https://validator.w3.org/) - 官方验证
- [HTML5 检查器](https://html5.validator.nu/) - 快速检查
- VS Code 插件：`HTML Validator`

---

## 📚 常用 HTML 实体

| 实体 | 符号 | 用途 |
|------|------|------|
| `&nbsp;` | 空格 | 不可断空格 |
| `&lt;` | < | 小于号 |
| `&gt;` | > | 大于号 |
| `&amp;` | & | 和号 |
| `&quot;` | " | 双引号 |
| `&apos;` | ' | 单引号 |
| `&copy;` | © | 版权符号 |
| `&reg;` | ® | 注册商标 |
| `&euro;` | € | 欧元符号 |

```html
<!-- 示例 -->
<p>5 &lt; 10</p>
<p>&copy; 2025 所有权利</p>
```

---

## 🎓 HTML 学习路径

1. **基础**：文档结构、标题、段落、列表
2. **文本格式**：粗体、斜体、强调、代码
3. **链接与图片**：`<a>`、`<img>`
4. **表格**：`<table>`、`<tr>`、`<td>`
5. **表单**：`<form>`、`<input>`、`<select>`
6. **语义化**：`<header>`、`<main>`、`<footer>`
7. **多媒体**：`<audio>`、`<video>`
8. **响应式**：视口、图片、媒体查询
9. **可访问性**：alt、label、语义标签
10. **最佳实践**：SEO、性能、代码规范

---

> **总结**：  
> HTML 是网页的骨架，掌握语义化标签和最佳实践是写好 HTML 的关键。

---

**上级索引**：[[HTML]] | [[计算机语言 MOC]] | [[前端开发 MOC]]
