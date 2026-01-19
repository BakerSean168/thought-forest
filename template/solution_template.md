---
tags:
  - tech/app/obsidian
  - type/snippet
  - status/evergreen
description: Obsidian问题解决方案模板
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[知识库管理体系 MOC]]

---

<%* 
// 初始化
let title = tp.file.title;
if (title.startsWith('Untitled')) {
title = await tp.system.prompt('Enter the title');
if (!title) return;
}
if (title == '') {
title = 'Untitled'
} else {
await tp.file.rename(title);
}
await tp.file.move('/z/' + title)
// 更新 updated
if (!tp.frontmatter.created) { tp.frontmatter.created = tp.file.creation_date("YYYY-MM-DDTHH:mm:ss"); } tp.frontmatter.updated = tp.date.now("YYYY-MM-DDTHH:mm:ss"); 
%>
# <% title %> 

## 问题详情
## 解决方案
## 实战经验
## 经验总结
## 信息参考
