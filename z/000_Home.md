---
tags:
  - type/moc
  - status/evergreen
description: 个人知识中枢 - 所有知识的入口页面
created: 2025-12-07T17:00:00
updated: 2025-12-08T12:00:00
---

# 🧠 个人知识中枢

> 这是知识库的主入口页面，通过这里可以快速导航到各个知识领域。

## 学科知识

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM "z"
WHERE contains(category, "discipline")

```

## 生活

### 购物

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/moc  AND #life/shopping 

```




## 📖 知识库管理

> 了解如何高效管理和使用本知识库

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/moc 
WHERE contains(category, "KnowledgeBase")
```


---

## 📊 知识库统计

> 使用 Dataview 动态查询所有 MOC 索引

```dataview
LIST
FROM #type/moc
SORT file.name ASC
```

