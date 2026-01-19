---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: JavaScript中-DOM
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# DOM

## 基础概念

文档对象模型（DOM，Document Object Model）是 HTML 和 XML 文档的编程接口。  
DOM 表示由多层节点构成的文档，通过它开发者可以添加、删除和修改页面的各个部分。

主要就是提供了使用 JavaScript 来操作 HTML 文档。

### 文档对象

Document Object Model, 文档对象模型

- 元素（element）  
  文档中的所有标签都是元素，元素可以看成是对象
- 节点（node）  
  文档中所有的内容都是节点：标签，属性，文本
- 文档（document）  
  一个页面就是一个文档

文档包含节点，节点包含元素

### 节点层级

任何 HTML 或 XML 文档都可以用 DOM 表示为一个由节点构成的层级结构。  
节点分很多类型，每种类型对应着文档中不同的信息和（或）标记，也都有自己不同的特性、数据和方法，而且与其他类型有某种关系。  
这些关系构成了层级，让标记可以表示为一个以特定节点为根的树形结构。

#### Node 类型

DOM Level 1 描述了名为 Node 的接口，这个接口是所有 DOM 节点类型都必须实现的。  
Node 接口在 JavaScript 中被实现为 Node 类型，在除 IE 之外的所有浏览器中都可以直接访问这个类型。  
在 JavaScript 中，所有节点类型都继承 Node 类型，因此所有类型都共享相同的基本属性和方法。

每个节点都有 nodeType 属性，表示该节点的类型。  
节点类型由定义在 Node 类型上的 12 个数值常量表示

1. nodeName 与 nodeValue
   nodeName 与 nodeValue 保存着有关节点的信息。这两个属性的值完全取决于节点类型。
2. 节点关系  
   文档中的所有节点都与其他节点有关系。
3. 操纵节点
4. 其他方法  
   cloneNode()  
	会返回与调用它的节点一模一样的节点。  
	normalize()  
	处理文档子树中的文本节点。

```js
// 取：
document.getElementById();

// 获取节点所有属性
node.attributes;

// 获取节点所有子元素
node.childNodes;

// 增：
let a = createElement("a");
document.body.append(a);

// 删：
document.body.removeChild(div);
```

#### Document 类型

Document 类型是 JavaScript 中表示文档节点的类型。  
在浏览器中，文档对象 document 是 HTMLDocument 的实例（HTMLDocument 继承 Document），表示整个 HTML 页面。  
document 是 window 对象的属性，因此是一个全局对象。

1. 文档子节点  
   虽然 DOM 规范规定 Document 节点的子节点可以是 DocumentType、Element、Processing- Instruction 或 Comment，但也提供了两个访问子节点的快捷方式。  
   documentElement 属性，始终指向 HTML 页面中的`<html>`元素。  
   document.body 属性，指向`<body>`
2. 文档信息
   title、URL、domain、referrer
3. 定位元素
4. 特殊集合
   document 对象上还暴露了几个特殊集合，这些集合也都是 HTMLCollection 的实例。  
   这些集合是访问文档中公共部分的快捷方式。  
	document.anchors 包含文档中所有带 name 属性的`<a>`元素。
5. DOM 兼容性检测
   由于 DOM 有多个 Level 和多个部分，因此确定浏览器实现了 DOM 的哪些部分是很必要的。
6. 文档写入
   document 对象有一个古老的能力，即向网页输出流中写入内容。  
   这个能力对应 4 个方法：write()、writeln()、open()和 close()。

#### Element 类型

Element 表示 XML 或 HTML 元素，对外暴露出访问元素标签名、子节点和属性的能力。

- nodeType = 1
- nodeName 值为元素的标签名
- nodeValue = null
- parentNode = Document || Element
- 子节点可以是 Element、Text、Comment、ProcessingInstruction、CDATASection、EntityReference 类型。

可以通过 nodeName 或 tagName 属性来获取元素的标签名。

#### Text 类型

Text 节点由 Text 类型表示，包含按字面解释的纯文本，也可能包含转义后的 HTML 字符，但不含 HTML 代码。

- nodeType = 3
- nodeName = "#text"
- nodeValue = 节点中包含的文本
- parentNode = Element 对象
- 不支持子节点

默认情况下，包含文本内容的每个元素最多只能有一个文本节点。  
只要开始标签和结束标签之间有内容，就会创建一个文本节点

#### Comment 类型

DOM 中的注释通过 Comment 类型表示。

- nodeType = 8
- nodeName= "#comment"
- nodeValue = 注释的内容
- parentNode = Document 或 Element 对象
- 不支持子节点

Comment 类型与 Text 类型继承同一个基类（CharacterData），因此拥有除 splitText()之外 Text 节点所有的字符串操作方法。  
与 Text 类型相似，注释的实际内容可以通过 nodeValue 或 data 属性获得。

#### CDATASection 类型

CDATASection 类型表示 XML 中特有的 CDATA 区块。  
CDATASection 类型继承 Text 类型，因此拥有包括 splitText()在内的所有字符串操作方法。

#### DocumentType 类型

DocumentType 类型的节点包含文档的文档类型（doctype）信息

#### DocumentFragment 类型

在所有节点类型中，DocumentFragment 类型是唯一一个在标记中没有对应表示的类型。  
DOM 将文档片段定义为“轻量级”文档，能够包含和操作节点，却没有完整文档那样额外的消耗。

#### Attr 类型

元素数据在 DOM 中通过 Attr 类型表示。  
Attr 类型构造函数和原型在所有浏览器中都可以直接访问。  
技术上讲，属性是存在于元素 attributes 属性中的节点。

##### 改变节点中的文本值

##### innerHTML、innerText、textContent

- innerHTML  
  表示所有 HTML  
  Element 对象的属性
- innerText  
  表示所有文本内容  
  HTMLElement 对象的属性
- textContent  
  返回的是字符串或 null  
  修改该属性会删除其他全部节点  
  Node 对象的属性

innerText 会触发回流，但是 textContent 可能不会触发回流，所以实际应用中，使用 textContent 性能更佳  
innerTTML 会返回 HTML 文本，textContent 的内容不会解析成 HTML 文本，使用 textContent 可以防止 XSS 攻击

### DOM 编程

动态脚本  
动态样式  
操作表格  
使用 NodeList

### MutationObserver 接口

可以在 DOM 被修改时异步执行回调。  
使用 MutationObserver 可以观察整个文档、DOM 树的一部分，或某个元素。  
此外还可以观察元素属性、子节点、文本，或者前三者任意组合的变化。

## DOM 扩展

### Selectors API

Selectors API Level 1 的核心是两个方法：querySelector()和 querySelectorAll()。  
在兼容浏览器中，Document 类型和 Element 类型的实例上都会暴露这两个方法。

### 元素遍历

### HTML5

#### CSS 类扩展

1. getElementsByClassName()
2. classList 属性

#### 焦点管理

document.activeElement，始终包含当前拥有焦点的 DOM 元素。  
document.hasFocus()方法，该方法返回布尔值，表示文档是否拥有焦点。

#### HTMLDocument 扩展

1. readyState 属性
   loading，表示文档正在加载  
   complete，表示文档加载完成
2. compatMode 属性  
   指示浏览器当前处于什么渲染模式
3. head 属性
   document.head 指向 head

#### 字符集属性

#### 自定义数据属性

#### 插入标记

#### scrollIntoView()

scrollIntoView()方法存在于所有 HTML 元素上，可以滚动浏览器窗口或容器元素以便包含元素进入视口。

这个方法可以用来在页面上发生某个事件时引起用户关注。  
把焦点设置到一个元素上也会导致浏览器将元素滚动到可见位置。

## DOM2 到 DOM3

DOM1（DOM Level 1）主要定义了 HTML 和 XML 文档的底层结构。  
DOM2（DOM Level 2）和 DOM3（DOM Level 3）在这些结构之上加入更多交互能力，提供了更高级的 XML 特性。  
实际上，DOM2 和 DOM3 是按照模块化的思路来制定标准的，每个模块之间有一定关联，但分别针对某个 DOM 子集。

```
 DOM Core：在DOM1核心部分的基础上，为节点增加方法和属性。
 DOM Views：定义基于样式信息的不同视图。
 DOM Events：定义通过事件实现DOM文档交互。
 DOM Style：定义以编程方式访问和修改CSS样式的接口。
 DOM Traversal and Range：新增遍历 DOM文档及选择文档内容的接口。
 DOM HTML：在DOM1 HTML部分的基础上，增加属性、方法和新接口。
 DOM Mutation Observers：定义基于 DOM变化触发回调的接口。这个模块是DOM4级模块，
用于取代Mutation Events。
```

## 使用

### 相关方法

```js
// 查询相关方法
getElementById();
getElementsByClassName();
getElementsByTagName();
querySelectorAll();
querySelector();
```

使用例子：

```js
// 取：
document.getElementById();

// 获取节点所有属性
node.attributes;

// 获取节点所有子元素
node.childNodes;

// 增：
let a = createElement("a");
document.body.append(a);

// 删：
document.body.removeChild(div);
```

## 现状

在使用 Vue 等框架时，好像已经不使用（不直接使用）DOM 接口了。  
框架通过虚拟 DOM 和 响应式数据绑定机制抽象了大部分 DOM 操作。

虚拟 DOM 算法，数据变化时：

- 生成新的虚拟 DOM 树
- 通过 Diff 算法比较新旧 DOM 树差异
- 更新必要部分

