---
tags:
  - tech/lang/html
  - type/concept
  - status/growing
description: HTML碎片化知识点
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端基础 MOC]] | [[HTML]]

---


# HTML碎片化知识点

## `<!DOCTYPE>`声明

**作用**  
声明文档类型，告诉浏览器以何种规范解析HTML文档。
不是HTML标签，而是文档类型声明（Document Type Declaration）。

**语法**
在HTML5中，唯一且简化的声明：`<!DOCTYPE html>`。  
在HTML4.01或XHTML中，声明更复杂（如 `<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">`）。

**特性**
必须位于HTML文档的第一行（在 `<html>` 标签之前）。
不区分大小写（如 `<!doctype html>` 也有效）。
不是单标签，也不是HTML标签，而是特殊声明。

## 回流和重绘

回流  
当render tree中的一部分或全部因为元素的规模尺寸、布局、隐藏等改变时，浏览器重新渲染部分DOM或全部DOM的过程。回流也被称为重排，其实从字面上来看，重排更容易让人形象易懂（即重新排版整个页面）。

重绘  
当页面元素样式改变不影响元素在文档流中的位置时（如background-color，border-color，visibility），浏览器只会将新样式赋予元素并进行重新绘制操作。

## src 和 href

| 对比维度 | src | href |
|---------|-----|------|
| 用途 | 嵌入资源（JS、图片等） | 建立链接（CSS、超链接等） |
| 加载行为 | 阻塞文档解析 | 并行加载，不阻塞解析 |
| 适用标签 | `<script>`、`<img>`、`<iframe>` | `<a>`、`<link>`、`<area>` |
| 核心特性 | 替换当前元素内容 | 关联外部资源 |

## 行内元素和块级元素

块级元素：  

- 块级元素主要用于构建页面结构
- 独占一行，从上到下垂直排列，前后自动换行
- 可以设置宽高，四个方向的内外边距，默认宽度为父元素的 100%

行内元素：  
- 行内元素通常用于修饰文本内容
- 不独占一行，从左到右水平排列，一行放不下才换行
- 宽高由内容决定，不能直接设置；只能设置左右方向内外边距；可以设置边框（border）和行高（line-height）

### 常见块级元素

`<div>` `<h1>`-`<h6>` `<p>` `<form>` `<table>` `<hr>` `<pre>`

### 常见行内元素

`<span>` `<a>` `<em>` `<i>` `<img>` `<input>`

### 行内块元素（inline-block）

兼具行内和块级元素的特性：  
- `<img>`
- `<input>`
- `<button>`
- `<textarea>`
- `<select>`

## URL 到 页面渲染

1. URL 解析  
  2. 提取协议、主机名、端口号、路径等信息。  
  3. DNS 查询  
4. 建立 TCP 连接  
5. 发送 HTTP 请求和服务器处理  
6. 浏览器解析与渲染流程  
7. JavaScript 执行与交互  
8. 断开 TCP 连接  

## 怎么渲染 http 文件到页面上

1. 构建 DOM 树  
2. 构建 CSSOM 树  
3. 生成渲染树  
4. 布局与绘制  

## 白屏问题的常见原因和排查

1. 网络和资源加载  
  页面资源未加载或加载超时  
  排查：  
	控制台打印错误、网络面板  
	测试CDN与资源路径  
	优化加载策略  
2. 前端代码错误  
3. 服务器与接口问题
4. 浏览器兼容与缓存问题  
5. 第三方服务异常

## js 的加载方式

同步加载  
  浏览器遇到 `<script>` 标签时，会暂停 HTML 解析，下载并执行脚本，完成后才继续渲染页面。  

异步加载
  defer  
  async  
  动态创建  

模块化加载  
  type="module"  

延迟加载  
  import()

## JavaScript 加载冲突

- 命名空间污染导致冲突  
  - 变量、函数名重复  
  - 第三方库冲突  
- 事件绑定冲突  
- 资源加载顺序问题  
- 浏览器兼容性冲突  
- DOM 和资源操作冲突  

## src 和 href

src 是要加载资源的路径  
href 是指定超链接目标地址 或 定义文档与外部资源的关联  

## 性能优化

### SRC

- 异步加载脚本：  
  - async 属性能让脚本与 HTML 解析并行加载，加载完后立即执行。  
  - defer 属性能让脚本与 HTML 解析并行加载，但执行顺序与页面解析顺序一致，且在解析完成后执行。  
- 图片懒加载：  
  loading="lazy"  
- CDN  
- 资源压缩  

### href

- 预加载关键资源  
  使用`<link rel='preload' href='style.css' as='style'>`指定关键资源，提高页面加载速度。  
- 防止阻塞渲染  
  将非关键 CSS 文件标记为延迟加载，`media='print' onload="this.media='all';"`  
- 动态加载  
  使用 JS 动态创建 `<link>` 标签加载非必要资源

## preload 和 prefetch

web 加载原语(如<link rel= preload > & <link rel= prefetch >)  

- preload 是一个声明式 fetch，可以强制浏览器在不阻塞 document 的 onload 事件的情况下请求资源。  
- Prefetch 告诉浏览器这个资源将来可能需要，但是什么时间加载这个资源是由浏览器来决定的。  

## Polyfill

Polyfill 是一块代码（通常是 Web 上的 JavaScript），用来为旧浏览器提供它没有原生支持的较新的功能。  

### 打补丁的三种方法

https://zhuanlan.zhihu.com/p/71640183

1. 手动打补丁  
2. 根据覆盖率自动打补丁
3. 根据浏览器特性，动态打补丁


