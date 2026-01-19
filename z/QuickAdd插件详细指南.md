---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian QuickAdd 插件 - 快速添加笔记与自动化完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[工作流与自动化]]

---

# QuickAdd 插件详细指南

> **核心功能**：快速添加笔记、自动化脚本、宏命令、模板触发  
> **难度级别**：⭐⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：必装（工作流自动化核心）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 用途 |
|-----|------|------|
| **多选菜单** | 快速选择操作 | 聚合多个命令 |
| **快速捕获** | 快速记录想法 | 思路不流失 |
| **宏脚本** | JavaScript 自动化 | 复杂操作自动化 |
| **模板触发** | 条件执行模板 | 智能创建笔记 |
| **用户输入** | 交互式输入框 | 动态数据集合 |
| **选项管理** | 多种执行方式 | 灵活配置 |
| **快捷键绑定** | 一键触发命令 | 提高效率 |

### 四种 QuickAdd 类型

| 类型 | 功能 | 用途 |
|-----|------|------|
| **Multi** | 多选菜单 | 汇聚多个命令 |
| **Capture** | 快速捕获 | 快速记录内容 |
| **Template** | 模板触发 | 创建结构化笔记 |
| **Macro** | 脚本宏 | 复杂自动化 |

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "QuickAdd"
3. 安装并启用

### 初始配置

```yaml
Settings → QuickAdd

## 基本设置
Manager Hotkey: Ctrl+Shift+K          # 打开管理器

## 默认位置
Default Folder for New Notes: "日记"

## 显示选项
Show in Ribbon: true                   # 显示快捷菜单
Show in Command Palette: true          # 显示在命令面板
```

---

## 快速开始

### 创建 Multi（多选菜单）

**Step 1：打开 QuickAdd 管理器**

```
Ctrl+Shift+K 或 左侧边栏 QuickAdd 按钮
```

**Step 2：创建新命令**

```
+ New 按钮
选择类型：Multi
设置名称：日常操作
```

**Step 3：添加选项**

```
在 Multi 中添加：
- [ ] 快速创建日记
- [ ] 快速创建任务
- [ ] 打开学习库
```

**Step 4：设置快捷键**

```
点击右侧快捷键图标
设置快捷键：如 Ctrl+Alt+Q
```

---

## 工作流示例

### 场景 1：日常快速操作菜单

**创建 Multi 类型命令**

```
名称：日常菜单
├─ 快速日记
├─ 快速任务
├─ 快速笔记
├─ 打开仓库
└─ 快速搜索
```

**使用流程**

```
1. Ctrl+Alt+Q 打开菜单
2. 选择 "快速日记"
3. 自动使用模板创建日记
4. 立即开始编写
```

### 场景 2：快速笔记捕获

**创建 Capture 类型命令**

```
名称：灵感记录

配置：
- Template: "Templates/idea"
- Append to: "Archive/Ideas"
- Input: 灵感标题
```

**使用示例**

```
突然有灵感 → 快捷键 → 输入标题 → 自动创建笔记 → 继续工作
```

### 场景 3：自动化书籍笔记

**创建 Template 类型命令**

```
名称：添加新书

配置：
- Template: "Templates/book"
- File Name: "{{PROMPT:书名}}"
- Folder: "Books"
```

**执行流程**

```
1. Ctrl+Shift+K
2. 选择 "添加新书"
3. 输入书名
4. 自动创建结构化笔记
```

### 场景 4：复杂自动化脚本

**创建 Macro 类型命令**

```javascript
// 名称：周总结自动生成

const date = new Date();
const weekStart = new Date(date.setDate(date.getDate() - date.getDay()));
const weekEnd = new Date(date.setDate(date.getDate() - date.getDay() + 6));

// 获取本周日记列表
const dailyNotesFolder = app.vault.getAbstractFileByPath("Daily Notes");
const notes = dailyNotesFolder.children.filter(f => {
  // 过滤本周的笔记
  return f.stat.mtime >= weekStart && f.stat.mtime <= weekEnd;
});

// 生成汇总内容
let content = `# 第 ${getWeekNumber()} 周总结\n\n`;
notes.forEach(note => {
  content += `- [[${note.basename}]]\n`;
});

// 创建周记
await app.vault.create("Weekly Notes/" + formatDate(weekStart) + ".md", content);
new Notice("周总结已生成");
```

---

## 常见问题

### Q1：如何创建带用户输入的命令？

**配置步骤**

```
1. 创建 Capture 或 Template 命令
2. 在文件名或内容中使用：
   {{PROMPT:提示文本}}
   {{PROMPT_VALUE:变量名}}

例子：
- 文件名：{{PROMPT:输入书名}}
  → 执行时弹出输入框
  → 用户输入内容替换占位符
```

**示例**

```
Template 名称：快速日记
文件名：{{DATE:YYYY-MM-DD}}
内容：
---
date: {{DATE:YYYY-MM-DD}}
title: "{{PROMPT:今日主题}}"
---

执行时：
1. 弹出 "输入今日主题"
2. 创建名为 2025-12-08 的笔记
3. title 字段自动填充用户输入
```

### Q2：如何在 Macro 中调用其他命令？

```javascript
// 在 Macro 中执行其他 QuickAdd 命令

app.commands.executeCommandById('quickadd:选择要执行的命令名');

// 或打开文件
const file = app.vault.getAbstractFileByPath("path/to/file.md");
app.workspace.getLeaf().openFile(file);
```

### Q3：如何列出本周的日记？

```javascript
// QuickAdd Macro 脚本

const dailyFolder = app.vault.getAbstractFileByPath("Daily Notes");
const today = new Date();
const weekStart = new Date(today.setDate(today.getDate() - today.getDay()));

let content = "## 本周日记\n";

dailyFolder.children.forEach(file => {
  const mtime = new Date(file.stat.mtime);
  if (mtime >= weekStart) {
    content += `- [[${file.basename}]]\n`;
  }
});

// 复制到剪贴板
navigator.clipboard.writeText(content);
new Notice("已复制到剪贴板");
```

### Q4：如何条件判断执行不同模板？

```javascript
// Macro 中的条件逻辑

const type = await QuickAdd.inputPrompt(
  "选择类型:",
  ["书籍", "文章", "视频"]
);

let template = "";
switch(type) {
  case "书籍":
    template = "Templates/book";
    break;
  case "文章":
    template = "Templates/article";
    break;
  case "视频":
    template = "Templates/video";
    break;
}

// 使用选定的模板创建笔记
QuickAdd.executeTemplate(template);
```

### Q5：如何与其他插件集成？

```javascript
// 在 Macro 中调用 Templater

// 执行 Templater 命令
app.commands.executeCommandById('templater-obsidian:templater-insert-template');

// 或执行 Periodic Notes 命令
app.commands.executeCommandById('periodic-notes:open-daily');
```

---

## 高级配置

### 1. 多层级菜单结构

**创建分类菜单**

```
Multi：主菜单
├─ Multi：工作流
│  ├─ Template：新建项目
│  ├─ Capture：快速任务
│  └─ Macro：生成报告
│
├─ Multi：学习
│  ├─ Template：新书笔记
│  ├─ Template：课程笔记
│  └─ Macro：生成学习计划
│
└─ Multi：生活
   ├─ Capture：灵感记录
   ├─ Template：旅行计划
   └─ Macro：周总结
```

**执行**

```
Ctrl+Q → 选择分类 → 选择具体命令 → 执行
```

### 2. 自定义 Prompt 类型

```javascript
// 日期选择器
const date = await QuickAdd.datePrompt(
  "选择日期:",
  new Date()
);

// 输入确认
const input = await QuickAdd.inputPrompt("输入内容:");

// 单选菜单
const choice = await QuickAdd.quickAddChoiceCommand(
  "选择选项:",
  ["选项1", "选项2", "选项3"]
);
```

### 3. 错误处理和日志

```javascript
try {
  // 执行操作
  const file = await app.vault.create("note.md", "content");
  new Notice("笔记创建成功");
} catch (error) {
  new Notice("错误: " + error.message);
  console.error("QuickAdd 错误:", error);
}
```

---

## 最佳实践

1. **模块化设计**
   - 不要创建太复杂的单个命令
   - 用 Multi 组织相关命令
   - 便于维护和理解

2. **清晰的命名**
   - 使用描述性名称
   - 便于快速识别
   - 避免缩写

3. **充分利用模板**
   - 结合 Templater
   - 减少重复代码
   - 提高执行速度

4. **定期备份**
   - 保存重要的宏脚本
   - 导出配置
   - 便于恢复和迁移

---

## 与其他插件配合

### QuickAdd + Templater

```markdown
QuickAdd 触发 Templater 模板：
Template 命令 → 指向 Templater 模板
模板中使用 Templater 语言进行动态生成
```

### QuickAdd + Periodic Notes

```javascript
// Macro 中触发 Periodic Notes

app.commands.executeCommandById('periodic-notes:open-daily');
// 或
app.commands.executeCommandById('periodic-notes:open-weekly');
```

### QuickAdd + Dataview

```javascript
// 在 Macro 中使用 Dataview 查询

const dv = this.app.plugins.plugins.dataview?.api;
const pages = dv.pages().where(p => p.tag === "#project");
// 使用查询结果进行操作
```

---

## 快速参考

### 常用变量

```
{{DATE:YYYY-MM-DD}}          # 当前日期
{{TIME:HH:mm}}               # 当前时间
{{PROMPT:提示文本}}           # 用户输入
{{VALUE:变量}}               # 变量值
{{VAULT}}                    # 仓库名
{{FILENAME}}                 # 当前文件名
```

### 常用快捷键

```
打开 QuickAdd 管理器：Ctrl+Shift+K
自定义快捷键：在管理器中设置
```

---

## 📚 相关文档

- [[Templater插件详细指南]] - 模板引擎详解
- [[Periodic Notes插件详细指南]] - 周期笔记
- [[Obsidian插件完全索引]] - 其他自动化插件

---

**上级索引**：[[Obsidian插件完全索引]] | [[工作流与自动化]]
