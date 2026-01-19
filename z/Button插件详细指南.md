---
tags:
  - tech/app/obsidian
  - type/howto
  - status/evergreen
description: Obsidian Button 插件 - 交互式按钮与自动化完全指南
created: 2025-12-08T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[Obsidian插件完全索引]] | [[工作流与自动化]]

---

# Button 插件详细指南

> **核心功能**：创建交互式按钮、执行命令、自动化操作、笔记内交互  
> **难度级别**：⭐⭐⭐  
> **推荐指数**：⭐⭐⭐⭐⭐  
> **必装程度**：强烈推荐（交互自动化）

---

## 功能概览

### 核心特性

| 功能 | 说明 | 用途 |
|-----|------|------|
| **按钮创建** | 在笔记中嵌入按钮 | 交互式操作 |
| **命令执行** | 点击执行 Obsidian 命令 | 快速操作 |
| **脚本执行** | 运行自定义脚本 | 复杂自动化 |
| **样式定制** | 按钮颜色、大小、样式 | 美化交互 |
| **条件判断** | 条件判断执行不同命令 | 智能操作 |
| **嵌入笔记链接** | 打开或链接笔记 | 导航便捷 |

### 按钮类型

| 类型 | 语法 | 用途 |
|------|------|------|
| **命令按钮** | `button-make-id-name` | 执行 Obsidian 命令 |
| **脚本按钮** | `button-script-code` | 执行 JavaScript |
| **打开链接** | `button-link-url` | 打开外部链接 |
| **笔记链接** | `button-note-[[]] ` | 打开笔记 |

---

## 安装与配置

### 安装

1. Settings → Community plugins → Browse
2. 搜索 "Button"
3. 安装并启用

### 基本配置

```yaml
Settings → Button

## 显示设置
Button Button Color: blue              # 按钮颜色
Button Button Size: medium             # 按钮大小
Show Button Icons: true                # 显示按钮图标

## 脚本权限
Allow Script Execution: true           # 允许脚本执行
```

---

## 快速开始

### 创建简单按钮

**基础语法**

```markdown
```button
name 按钮名称
type command
action app.commands.executeCommandById('command-id')
```
```

**实际示例**

```markdown
```button
name 打开日记
type command
action app.commands.executeCommandById('periodic-notes:open-daily')
```
```

### 创建脚本按钮

**示例：创建新笔记按钮**

```markdown
```button
name 新建笔记
type script
action
​```javascript
await app.vault.create("笔记.md", "# 新笔记\n\n开始编写...");
app.workspace.openLinkText("笔记.md", "", false);
new Notice("笔记已创建");
​```
```
```

---

## 工作流示例

### 场景 1：学习仪表板

**创建学习控制面板**

```markdown
# 学习管理面板

## 快速操作

```button
name 📅 打开今日日记
type command
action app.commands.executeCommandById('periodic-notes:open-daily')
```

```button
name 📚 打开学习库
type command
action app.workspace.openLinkText("学习", "", false)
```

```button
name 📊 打开学习进度
type command
action app.commands.executeCommandById('dataview:run-dataview-query')
```

## 今日学习任务

```
使用 Tasks 插件管理
按钮提供快速打开
```

## 学习时间统计

```dataview
LIST
FROM "Daily Notes"
WHERE file.mtime >= date(today) - dur(7 days)
```
```

### 场景 2：项目管理面板

```markdown
# 项目管理

## 项目操作

```button
name ➕ 创建新项目
type script
action
​```javascript
const projectName = await app.vault.adapter.prompt("项目名称:", "");
if (projectName) {
  const folderPath = `Projects/${projectName}`;
  await app.vault.createFolder(folderPath);
  const notePath = `${folderPath}/index.md`;
  await app.vault.create(notePath, `# ${projectName}\n\n## 项目信息\n\n## 任务列表\n`);
  app.workspace.openLinkText("index.md", folderPath, false);
  new Notice(`项目 ${projectName} 已创建`);
}
​```
```

```button
name 📋 查看所有项目
type command
action app.workspace.openLinkText("Projects", "", false)
```

## 活跃项目

- [[Project A]]
- [[Project B]]
- [[Project C]]
```

### 场景 3：快速笔记创建

```markdown
```button
name 📝 灵感记录
type script
action
​```javascript
const idea = await app.vault.adapter.prompt("灵感内容:", "");
if (idea) {
  const date = new Date().toLocaleDateString('zh-CN');
  const fileName = `Ideas/${date}-想法.md`;
  const content = `# 灵感记录\n\n日期: ${date}\n\n${idea}`;
  await app.vault.create(fileName, content);
  new Notice("灵感已记录");
}
​```
```

```button
name 🔗 快速链接
type script
action
​```javascript
const link = await app.vault.adapter.prompt("输入链接:", "");
const title = await app.vault.adapter.prompt("链接标题:", "");
const content = `[${title}](${link})`;
navigator.clipboard.writeText(content);
new Notice("链接已复制");
​```
```
```

---

## 常见问题

### Q1：如何找到命令的 ID？

**查找方法**

```
1. Settings → Hotkeys
2. 搜索要执行的命令
3. 右侧显示命令 ID
4. 复制到按钮的 action 中
```

**常用命令 ID 列表**

```
打开日记：periodic-notes:open-daily
打开周记：periodic-notes:open-weekly
打开月记：periodic-notes:open-monthly
新建笔记：file:create-new
打开快速切换：switcher:open
打开搜索：editor:open-search
```

### Q2：如何在按钮中获取用户输入？

**输入方法**

```javascript
// 单行输入
const input = await app.vault.adapter.prompt("提示文本:", "默认值");

// 多选列表
const choice = await app.vault.adapter.confirm("确认操作?");

// 获取当前笔记
const activeFile = app.workspace.activeLeaf?.view?.file;
const fileName = activeFile?.basename;
```

### Q3：如何添加按钮样式？

**样式配置**

```markdown
​```button
name 彩色按钮
type command
action app.commands.executeCommandById('command-id')
color blue
size medium
style primary
​```
```

**可用选项**

```
color: blue, red, green, yellow, purple, orange
size: small, medium, large
style: primary, secondary, success, danger, warning
```

### Q4：如何在按钮中执行多个命令？

```javascript
​```javascript
// 顺序执行多个命令
app.commands.executeCommandById('command1');
app.commands.executeCommandById('command2');
app.commands.executeCommandById('command3');

// 或等待后执行
await app.commands.executeCommandById('command1');
new Notice("第一个命令执行完成");
await app.commands.executeCommandById('command2');
​```
```

### Q5：如何处理错误？

```javascript
try {
  const file = await app.vault.create("note.md", "content");
  new Notice("操作成功");
} catch (error) {
  new Notice("错误: " + error.message);
  console.error("Button 错误:", error);
}
```

---

## 高级技巧

### 1. 创建数据录入面板

```markdown
# 数据录入

```button
name 添加新项目
type script
action
​```javascript
const title = await app.vault.adapter.prompt("项目名称:", "");
const priority = await app.vault.adapter.prompt("优先级 (1-3):", "2");
const deadline = await app.vault.adapter.prompt("截止日期 (YYYY-MM-DD):", "");

if (title) {
  const content = `---
title: ${title}
priority: ${priority}
deadline: ${deadline}
status: planning
---

# ${title}

## 项目详情

## 进展情况

## 完成标准`;
  
  await app.vault.create(`Projects/${title}.md`, content);
  new Notice("项目已添加");
}
​```
```

### 2. 创建快捷导航面板

```markdown
# 快速导航

```button
name 📖 学习笔记
type command
action app.workspace.openLinkText("学习笔记", "", false)
```

```button
name 💼 工作项目
type command
action app.workspace.openLinkText("Projects", "", false)
```

```button
name 📝 日记
type command
action app.commands.executeCommandById('periodic-notes:open-daily')
```

```button
name 🔍 全局搜索
type command
action app.commands.executeCommandById('editor:open-search')
```
```

### 3. 条件按钮（基于当前笔记）

```javascript
const activeFile = app.workspace.activeLeaf?.view?.file;
if (activeFile) {
  const fileName = activeFile.basename;
  new Notice(`当前笔记: ${fileName}`);
  // 根据文件名执行不同操作
}
```

---

## 最佳实践

1. **按钮命名清晰**
   - 使用 emoji 图标增加识别度
   - 名称简洁明了
   - 避免歧义

2. **组织按钮布局**
   - 相关按钮分组
   - 常用按钮置顶
   - 使用分隔符分区

3. **错误处理**
   - 用 try-catch 捕捉错误
   - 给用户反馈消息
   - 记录日志便于调试

4. **性能考虑**
   - 避免复杂计算
   - 异步操作用 async/await
   - 不要阻塞主线程

---

## 与其他插件配合

### Button + QuickAdd

```markdown
Button 提供可视按钮，QuickAdd 提供后台命令
结合使用获得最强自动化能力
```

### Button + Templater

```markdown
```button
name 使用模板创建
type script
action
​```javascript
app.commands.executeCommandById('templater-obsidian:templater-insert-template');
​```
```
```

### Button + Periodic Notes

```markdown
```button
name 打开今日日记
type command
action app.commands.executeCommandById('periodic-notes:open-daily')
```
```

---

## 快速参考

### 常用脚本片段

```javascript
// 打开笔记
app.workspace.openLinkText("笔记名", "", false);

// 创建笔记
await app.vault.create("文件夹/笔记.md", "内容");

// 获取当前笔记
const activeFile = app.workspace.activeLeaf?.view?.file;

// 显示通知
new Notice("消息内容");

// 执行命令
app.commands.executeCommandById('command-id');

// 用户输入
const input = await app.vault.adapter.prompt("提示:", "默认值");
```

---

## 📚 相关文档

- [[QuickAdd插件详细指南]] - 快速添加命令
- [[Templater插件详细指南]] - 模板自动化
- [[Obsidian插件完全索引]] - 其他自动化插件

---

**上级索引**：[[Obsidian插件完全索引]] | [[工作流与自动化]]
