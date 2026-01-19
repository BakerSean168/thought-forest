---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Templater 插件 - 高级模板与脚本自动化完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[笔记与编辑]]

---

# Templater 插件详细指南

> **核心功能**：基于 JavaScript 的高级模板引擎，支持动态内容、变量替换、脚本自动化  
> **难度级别**：⭐⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：必装（自动化必备）

---

## 📋 快速导航

- [功能概览](#功能概览)
- [安装与配置](#安装与配置)
- [基础用法](#基础用法)
- [高级特性](#高级特性)
- [常用脚本示例](#常用脚本示例)
- [常见问题](#常见问题)

---

## 功能概览

### 核心特性

| 功能 | 说明 | 使用场景 |
|-----|------|---------|
| **变量替换** | 自动填充日期、标题、选中文本等 | 快速创建结构化笔记 |
| **JavaScript 脚本** | 编写自定义逻辑与自动化 | 复杂的笔记生成 |
| **条件语句** | if/else 逻辑判断 | 动态内容选择 |
| **循环** | for/while 循环生成重复内容 | 批量生成列表项 |
| **函数调用** | 调用 Node.js API | 高级自动化 |
| **用户输入** | 交互式输入提示 | 动态参数收集 |

### Templater vs Obsidian 原生模板

| 特性 | Obsidian 模板 | Templater |
|------|--------------|-----------|
| 日期变量 | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| 自定义脚本 | ❌ | ⭐⭐⭐⭐⭐ |
| JavaScript | ❌ | ⭐⭐⭐⭐⭐ |
| 条件判断 | ❌ | ⭐⭐⭐⭐ |
| 用户交互 | ❌ | ⭐⭐⭐⭐ |
| 学习成本 | 低 | 中-高 |

---

## 安装与配置

### 安装插件

1. **打开社区插件浏览器**
   - Settings → Community plugins → Browse

2. **搜索并安装**
   - 搜索 "Templater"
   - 点击 Install → Enable

3. **允许执行脚本**
   - Settings → Templater → 勾选"Script files folder location"

### 基础配置

**Settings → Templater**

```yaml
# 模板文件夹路径
Template folder location: "templates"

# 快捷键
Trigger templater: Alt+E (从当前笔记触发)
Open insert template modal: Alt+T (打开模板选择)

# 脚本文件夹（可选）
Script files folder location: "scripts"

# 输出路径
Trigger template in active file: ✅

# 其他选项
System command timeout: 5 (秒)
Empty file template: (可选，新文件默认模板)
```

---

## 基础用法

### 内置变量

| 变量 | 说明 | 示例 |
|-----|------|------|
| `{{date}}` | 当前日期 | 2025-12-08 |
| `{{date:YYYY-MM-DD}}` | 自定义日期格式 | 2025-12-08 |
| `{{time}}` | 当前时间 | 14:30:45 |
| `{{title}}` | 笔记标题 | My Note |
| `{{file_name}}` | 文件名 | my-note |
| `{{file_path}}` | 文件路径 | z/my-note.md |
| `{{file_folder}}` | 文件夹路径 | z/ |

### 简单模板示例

**创建文件** `templates/article.md`

```markdown
---
title: "{{title}}"
created: {{date:YYYY-MM-DD HH:mm:ss}}
updated: {{date:YYYY-MM-DD HH:mm:ss}}
tags:
  - 
---

# {{title}}

> 一句话总结

## 导读

## 内容

## 总结

---

标签：#draft
```

**使用方法**

```
1. 打开命令面板：Ctrl+P
2. 搜索：Templater: Create new note from template
3. 选择 article.md
4. 输入笔记标题
5. 自动生成笔记，日期和标题已填充
```

---

## 高级特性

### 1. JavaScript 变量

**语法**：`<% js 代码 %>`

```markdown
# <% tp.file.title %>

创建时间：<% tp.date.now("YYYY-MM-DD HH:mm") %>
最后修改：<% tp.file.creation_date("YYYY-MM-DD") %>
文件夹：<% tp.file.folder("") %>
```

### 2. 条件判断

```markdown
<% if (tp.file.title.includes("日记")) { %>
## 每日反思
- 今天的收获：
- 明天的计划：
<% } else { %>
## 标准笔记结构
## 背景
## 内容
## 总结
<% } %>
```

### 3. 循环生成

```markdown
## 本周任务

<% for (let i = 1; i <= 7; i++) { %>
- [ ] 周<% 
  const days = ['一', '二', '三', '四', '五', '六', '日'];
  %><%= days[i-1] %>的任务
<% } %>
```

### 4. 用户输入提示

```markdown
<% 
  const author = await tp.system.prompt("作者名称");
  const category = await tp.system.prompt("内容分类");
%>

---
author: <% author %>
category: <% category %>
---
```

### 5. 文件操作

```markdown
最近修改的文件：
<% 
  const recent = await tp.system.listFiles("z/");
  const sorted = recent.sort((a, b) => 
    tp.file.creation_date(b) - tp.file.creation_date(a)
  );
%>
<% for (let i = 0; i < 5; i++) { %>
- [[<% sorted[i] %>]]
<% } %>
```

### 6. 插入其他模板

```markdown
# 主模板

<% await tp.file.include("[[templates/header]]") %>

主体内容...

<% await tp.file.include("[[templates/footer]]") %>
```

---

## 常用脚本示例

### 脚本 1：自动生成日记

`templates/daily.md`

```markdown
---
title: "{{date:YYYY-MM-DD}} 日记"
created: {{date:YYYY-MM-DD HH:mm:ss}}
type: "daily"
mood: ""
tags:
  - daily
  - {{date:YYYY-MM}}
---

# <%+ tp.date.now("YYYY-MM-DD dddd") %>

> 每日反思与总结

## 📅 今日计划

- [ ] 任务 1
- [ ] 任务 2
- [ ] 任务 3

## 📝 今日日志

### 上午

### 下午

### 晚上

## 💡 今日灵感

- 想法 1
- 想法 2

## 📊 今日数据

- 完成任务: ? / 3
- 学习时间: ? 小时
- 阅读时间: ? 分钟

## 🎯 明日重点

- 

## 📌 反思

> 今天最大的收获是什么？

---

**心情**: <%+ await tp.system.prompt("今天的心情 (😊/😐/😢)") %>
```

### 脚本 2：文章模板（带目录）

`templates/article.md`

```markdown
---
title: "<%+ await tp.system.prompt("文章标题") %>"
author: "Your Name"
date: <%+ tp.date.now("YYYY-MM-DD") %>
updated: <%+ tp.date.now("YYYY-MM-DD") %>
category: "<%+ await tp.system.prompt("分类 (Tech/Life/Reading)") %>"
tags:
  - <%+ await tp.system.prompt("标签 (用逗号分隔)") %>
status: "draft"
---

# <%+ tp.file.title %>

> <%+ await tp.system.prompt("一句话总结") %>

## 导读

## 正文

### 

## 总结

---

**相关链接**：
- [[]]
```

### 脚本 3：获取最近笔记列表

`templates/recent-notes.md`

```markdown
# 最近修改的笔记

<% 
  const files = await tp.system.listFiles("z/");
  const mdFiles = files.filter(f => f.endsWith('.md'));
  const sorted = mdFiles
    .map(f => ({
      name: f.replace('.md', ''),
      path: `z/${f}`
    }))
    .slice(0, 10);
%>

<% for (let file of sorted) { %>
- [[<% file.name %>]]
<% } %>
```

### 脚本 4：动态链接插入

`templates/with-links.md`

```markdown
---
title: "<%+ tp.file.title %>"
created: <%+ tp.date.now("YYYY-MM-DD") %>
related:
  - 
---

# <%+ tp.file.title %>

<% 
  const relatedPrompt = await tp.system.prompt(
    "相关文件 (用逗号分隔)"
  );
  const related = relatedPrompt
    .split(',')
    .map(s => s.trim())
    .filter(s => s.length > 0);
%>

## 相关笔记

<% for (let note of related) { %>
- [[<% note %>]]
<% } %>
```

---

## API 参考

### tp.date（日期操作）

```javascript
tp.date.now("YYYY-MM-DD")           // 当前日期
tp.date.now("HH:mm:ss")             // 当前时间
tp.date.now("dddd")                 // 星期几 (Monday)
tp.date.now("ddd")                  // 星期几 (Mon)
tp.file.creation_date("YYYY-MM-DD") // 创建日期
tp.file.last_update_date("YYYY")    // 最后修改日期
```

### tp.file（文件操作）

```javascript
tp.file.title                   // 文件标题
tp.file.path("relative")        // 相对路径
tp.file.folder("relative")      // 文件夹路径
tp.file.name("include_extension") // 文件名
tp.file.exists("path")          // 检查文件是否存在
```

### tp.system（系统交互）

```javascript
await tp.system.prompt("提示文本")        // 用户输入
await tp.system.listFiles("folder")      // 列出文件夹
await tp.system.suggester(array, items)  // 下拉选择
```

### tp.obsidian（Obsidian API）

```javascript
await tp.obsidian.getActiveFile()        // 获取当前文件
await tp.obsidian.getActiveView()        // 获取当前视图
await tp.obsidian.existsOpenFile("path") // 检查文件是否打开
```

---

## 常见问题

### Q1：如何调试脚本错误？

**打开开发者工具**

```
Settings → About → 检查"Show performance" 和"Enable debug mode"
View → Toggle Developer Tools (Ctrl+Shift+I)
```

**查看错误**

```
点击 Console 标签页，查看错误信息
通常会显示第几行出错
```

### Q2：如何在脚本中获取用户输入？

```markdown
<% 
  const input = await tp.system.prompt("请输入内容");
  if (!input) {
    // 用户取消
  }
%>
用户输入了：<% input %>
```

### Q3：如何在脚本中读取其他文件内容？

```javascript
const vault = tp.obsidian.app.vault;
const file = await vault.getAbstractFileByPath("path/to/file.md");
const content = await vault.read(file);
```

### Q4：如何生成随机内容？

```markdown
<% 
  const quotes = [
    "坚持就是胜利",
    "每天进步一点点",
    "生活就是实验"
  ];
  const random = quotes[Math.floor(Math.random() * quotes.length)];
%>

今日鸡汤：<% random %>
```

### Q5：脚本太复杂，怎么办？

**拆分成多个函数**

```javascript
<% 
  function getGreeting() {
    const hour = new Date().getHours();
    if (hour < 12) return "早上好";
    if (hour < 18) return "下午好";
    return "晚上好";
  }
  
  const greeting = getGreeting();
%>

<% greeting %>，祝你有美好的一天！
```

### Q6：如何实现动态日期（相对日期）？

```markdown
<% 
  const today = tp.date.now("YYYY-MM-DD");
  const tomorrow = new Date();
  tomorrow.setDate(tomorrow.getDate() + 1);
  const tomorrowStr = tp.date.now(
    "YYYY-MM-DD", 
    tomorrow
  );
%>

今天：<% today %>
明天：<% tomorrowStr %>
```

---

## 最佳实践

1. **模板放在独立文件夹**
   ```
   templates/
   ├── article.md
   ├── daily.md
   ├── reading.md
   └── project.md
   ```

2. **为模板添加标准元数据**
   ```yaml
   ---
   template_type: "article"
   version: 1.0
   ---
   ```

3. **使用提示收集信息**
   ```
   避免硬编码的值，使用 prompt 让用户输入
   ```

4. **测试脚本**
   ```
   创建一个测试笔记，验证模板正确运行
   ```

5. **记录脚本说明**
   ```
   在模板顶部添加注释，解释各个变量的用途
   ```

---

## 📚 相关文档

- [[Linter插件详细指南]] - 配合 Templater 的自动格式化
- [[Periodic Notes插件详细指南]] - 周期性笔记自动创建
- [[Obsidian写作工作流]] - 使用 Templater 的完整写作系统

---

**上级索引**：[[Obsidian插件完全索引]] | [[笔记与编辑]]
