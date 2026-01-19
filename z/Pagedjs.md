---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: Paged.js分页打印库
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[JavaScript MOC]] | [[打印功能 MOC]]

---


# Paged.js 技术知识清单

## 1. 概览（Overview）

- **这是一个什么东西？** Paged.js 是一个开源的 JavaScript 库，它实现了 W3C 的“分页媒体 (Paged Media)”相关 CSS 规范（例如 CSS Paged Media Module Level 3），充当了浏览器对这些规范的**polyfill**（垫片）。它在浏览器中将传统的 HTML 滚动内容转换为分页布局。
- **它解决什么问题？适用场景是什么？** 它解决了使用标准 HTML/CSS 进行复杂印刷品（如书籍、报告、宣传册等）设计时的布局限制。
	- **适用场景：**
		
		1. **Web 混合出版：** 从单一的 HTML 源文件生成印刷品、PDF 文件、电子书等。
		2. **动态 PDF 生成：** 需要根据数据动态生成具有复杂版式和页眉/页脚的专业级 PDF 文档。
		3. **浏览器内预览：** 在浏览器中实时预览印刷品的最终分页和布局效果。
			
- **一句话总结** Paged.js 是一个将 HTML/CSS 转换为印刷级分页文档的 JavaScript 引擎。

## 2. 关键概念（Core Concepts）

- **概念 A：Paged Media CSS (分页媒体 CSS)**
	- **说明：** 这是 Paged.js 的核心。它指的是 W3C CSS 规范中用于控制页面布局和打印的模块，例如 `@page` 规则。
	- **示例：** 使用 `@page { size: A4; margin: 25mm; }` 来定义页面的尺寸和边距。
- **概念 B：Generated Content (生成内容)**
	- **说明：** 允许在 HTML 内容之外自动生成内容，主要用于创建页码、运行页眉/页脚、目录等。
	- **相关属性：** `content`、`string-set`（设置运行页眉/页脚的内容）、`counter-reset` 和 `counter-increment`（用于页码和章节编号）。
- **专有名词 / 缩写解释**
	- **Polyfill：** Paged.js 作为浏览器对 W3C Paged Media 规范的实现垫片，使现代浏览器能够处理这些 CSS 规则。
	- **Previewer：** Paged.js 提供的核心 API 类，负责解析 HTML、CSS 并执行分页和布局流程。
	- **Handlers：** 用于扩展 Paged.js 功能的模块。你可以通过自定义 Handler 拦截渲染流程中的不同阶段（如内容分块、CSS 样式化、页面布局后），执行自定义的 JavaScript 逻辑。

## 3. 安装与环境准备（Installation / Setup）

- **系统要求**
	- 一个支持现代 JavaScript 和 CSS 的 Web 浏览器。
	- 如果需要生成 PDF 文件，通常需要 Node.js 和 NPM。
- **安装步骤（命令行/GUI）**
	
	1. **作为 Polyfill (CDN)：** 最简单的方法，直接在 HTML 文档的 `<head>` 中引入 CDN 脚本，它会在页面加载完成后自动运行。

		```
        <script src="[https://unpkg.com/pagedjs/dist/paged.polyfill.js](https://unpkg.com/pagedjs/dist/paged.polyfill.js)"></script>
        ```

	2. **作为 NPM 模块：** 在项目中进行编程控制或自定义 Handlers 时使用。

		```
        npm install pagedjs
        ```

	3. **CLI 工具 (用于生成 PDF)：** 依赖于无头浏览器（如 Puppeteer），可直接在命令行生成 PDF。

		```
        npm install -g pagedjs-cli
        ```

- **环境变量 / 配置说明**
	- **全局配置 (禁用自动运行):** 如果需要手动控制分页时机，可以在加载 polyfill 脚本之前设置全局配置。

		```
        <script>
            window.PagedConfig = { auto: false };
        </script>
        <script src="[https://unpkg.com/pagedjs/dist/paged.polyfill.js](https://unpkg.com/pagedjs/dist/paged.polyfill.js)"></script>
        // 稍后调用 window.PagedPolyfill.preview() 来启动
        ```

## 4. 快速开始（Quick Start）

- **最小可运行示例** 创建一个 `index.html` 和一个 `style.css` 文件。
- **基本使用流程**
	
	1. 编写包含内容的 HTML。
	2. 编写包含分页媒体 CSS 规则（`@page`）的 CSS。
	3. 在 HTML 中引入 Paged.js Polyfill 脚本。
	4. 浏览器加载页面后，内容将自动转换为分页视图。
	5. 使用浏览器的“打印”功能（选择“另存为 PDF”）即可获得最终 PDF。
		
- **示例代码 / 指令** **HTML (`index.html`):**

	```
    <!DOCTYPE html>
    <html>
    <head>
        <title>Paged.js 示例</title>
        <link rel="stylesheet" href="style.css">
        <script src="[https://unpkg.com/pagedjs/dist/paged.polyfill.js](https://unpkg.com/pagedjs/dist/paged.polyfill.js)"></script>
    </head>
    <body>
        <h1>第一章标题</h1>
        <p>这是第一页的内容...</p>
        <div class="chapter-start">第二章标题</div>
        <p>这是第二页的内容...</p>
    </body>
    </html>
    ```

	**CSS (`style.css`):**

	```
    @page {
        size: A5; /* 定义页面尺寸 */
        margin: 20mm; /* 定义页面边距 */
        @top-center {
            content: "我的文档"; /* 在顶部中心添加静态内容 */
        }
    }
    .chapter-start {
        break-before: page; /* 强制在此元素之前分页 */
        font-size: 1.5em;
        margin-top: 50%;
        text-align: center;
    }
    ```

	**CLI 指令 (生成 PDF):**

	```
    pagedjs-cli index.html -o result.pdf
    ```

## 5. 进阶使用（Advanced Usage）

- **配置项说明**
	- **`Previewer` API：** 使用 `import { Previewer } from 'pagedjs';` 可以获得更细粒度的控制，例如手动传入 DOM 内容、CSS 路径和输出容器。

		```
        import { Previewer } from 'pagedjs';
        let paged = new Previewer();
        paged.preview(document.body.innerHTML, ["path/to/my.css"], document.body)
            .then((flow) => {
                console.log(`总页数: ${flow.total}`);
            });
        ```

- **可扩展点 / 插件机制 (Handlers)**
	- 你可以创建自定义 Handler 来扩展或修改分页行为。Handler 提供了多个生命周期钩子（hooks）。
	- **示例钩子：**
		- `beforeContent()`：在内容分块开始前执行。
		- `afterPageLayout(pageFragment, page)`：在页面内容布局完成后执行，可用于修改或检查特定页面的 DOM 结构。
- **常见模式（patterns）**
	- **运行页眉 (Running Headers/Footers)：** 使用 `string-set` 属性将内容（如章节标题）提取到变量中，然后通过 `@page` 规则中的 `@top-center` 或 `@bottom-left` 等 margin box 区域，使用 `content: string(variable-name)` 将其显示为页眉或页脚。
	- **重复表格头：** Paged.js 实现了 CSS 的 `table-header-group` 特性，允许表格头部 (`<thead>`) 在跨页时自动重复。

## 6. 目录结构（Project Structure）

对于使用 Paged.js 的项目，典型的结构更侧重于**数据**、**模板**和**样式**的分离。

```
book-project/
  ├── data/                # 原始数据或Markdown文件 (如果从数据生成内容)
  ├── src/                 # 源码，可能包含JS模块或Web组件
  ├── templates/           # HTML 模板文件
  ├── styles/
  │   ├── paged.css      # 包含 @page 规则、Generated Content 的核心样式
  │   └── common.css     # 基础 Web 样式
  └── index.html           # 主入口文件，用于加载内容和 Paged.js 脚本
```

- **`data/` 的作用说明：** 存放书籍的原始 Markdown、XML 或 JSON 数据。
- **`templates/` 的作用说明：** 存放用于将数据转换为最终 HTML 结构的模板文件（可能使用 Nunjucks, Handlebars 等模板引擎）。
- **`styles/paged.css` 的作用说明：** 存放专门针对打印媒体的 CSS 规则，这是 Paged.js 引擎主要关注的文件。
- **`index.html` 的作用说明：** 整合所有生成的 HTML 内容、CSS 链接和 Paged.js 脚本，作为 CLI 或 Previewer 的输入源。

## 7. 常见问题（FAQ）

- **Q1: Paged.js 和传统的 Web 打印有什么区别？** A: 传统的 Web 打印（`@media print`）功能非常有限，无法控制页眉/页脚、跨页内容（如表格头重复）或精确的分页。Paged.js 通过实现 W3C Paged Media 规范，提供了这些专业级排版所需的全部功能。
- **Q2: 为什么我用浏览器打印出来的 PDF 效果不好？** A: 浏览器自带的打印功能可能会覆盖 Paged.js 的一些布局。打印时**必须**确保以下设置：禁用**页眉和页脚**，启用**背景图形**（以便显示颜色和边框），并且将**边距**设置为“无”（或让 Paged.js 的 `@page` 规则来控制）。

## 8. 排错指南（Debug / Troubleshooting）

- **常见报错 & 解决方法**
	- **问题：** `@page` 规则不生效。
		- **解决方法：** 检查你的 CSS 文件是否正确链接到 HTML。Paged.js 对 CSS 解析要求较高，确保你的 `@page` 规则没有语法错误。
	- **问题：** 页面内容溢出或留白过多。
		- **解决方法：** 检查你的 CSS 中是否有固定宽度的元素（如设置了固定的 `px` 宽度）。尝试使用 CSS 的 `break-before: page;` 或 `break-after: page;` 来手动调整章节的分页位置。
- **检查顺序**
	
	1. 确认 Paged.js 脚本是否正确加载（检查浏览器 Console 是否有 Paged.js 相关的输出）。
	2. 确认 `@page` 规则是否被正确解析（通过浏览器开发者工具检查应用的样式）。
	3. 使用 `console.log()` 检查自定义 Handler 中的逻辑是否按预期执行。

## 9. 最佳实践（Best Practices）

- **推荐的工作流**
	- **开发环境：** 使用 Paged.js Polyfill 在浏览器中实时预览排版效果，并使用开发者工具进行调试。
	- **生产环境：** 部署到服务器上，通过 `pagedjs-cli` 或基于 Puppeteer/Chromium 的服务（如 Doppio.sh）来调用 Paged.js 进行无头 PDF 生成，以确保一致且可靠的输出。
- **最常见的踩坑**
	- **避免使用 `background-image` 或 `box-shadow` 在整个页面上：** Paged Media CSS 对背景和阴影的处理与普通 Web 视图不同，尤其是在需要背景打印时。
	- **不要依赖浏览器的默认分页：** 始终使用 CSS `break-after` 或 `break-before` 属性来控制章节、标题、图片的分页，确保逻辑断点正确。
- **性能/安全注意点**
	- **优化内容加载：** 确保在 Paged.js 启动前，所有资源（图片、字体）都已加载完毕，否则可能导致分页错误。
	- **简化 DOM 结构：** 复杂的嵌套 DOM 结构会显著增加 Paged.js 的处理时间，尽量保持内容 HTML 结构的简洁。

## 10. 资源与参考（Resources）

- **官方文档**
	- [Paged.js 官方网站](https://pagedjs.org/ "null")
	- [Paged.js 文档](https://www.google.com/search?q=https://pagedjs.org/documentation/ "null")
- **视频/博客**
	- 可以搜索关键词 "Paged.js tutorial" 或 "Web to Print CSS" 找到相关教程和演示视频。
- **GitHub 示例**
	- [Paged.js GitHub 仓库](https://github.com/pagedjs/pagedjs "null")

## 11. 个人笔记（Personal Notes）

- **使用心得**
	- 理解 Paged Media CSS 是关键。它与普通的 Web CSS 有很大区别，重点在于盒子模型和流（Flow）的概念。
	- 在设计运行页眉/页脚时，要精确掌握 `string-set` 的作用范围和时机，这是实现动态目录和页码的关键。
- **自定义小技巧**
	- 在开发时，可以利用浏览器的开发者工具，直接在分页后的 DOM 结构上进行 CSS 调试，帮助理解分页是如何影响 HTML 元素的。
- **后续学习方向**
	- 深入研究如何编写自定义 `Handlers`，以应对 W3C 规范中未涵盖的复杂排版需求（如特定的脚注处理、目录生成）。
	- 探索将其与静态网站生成器（如 Eleventy 或 Jekyll）结合，创建自动化出版工作流。

pagedjs 就是一个补丁用于给浏览器提供应该实现但不支持的 css 特性。

pagedjs 的分页内容默认不是渲染在原 DOM 上，而是生成新的 DOM：
- 分页后的 DOM 放在 `.pagedjs_pages`
- 原始 DOM 仍然保留
- pagedjs 的分页用的是 Shadow DOM-like 的结构 + polyfills
