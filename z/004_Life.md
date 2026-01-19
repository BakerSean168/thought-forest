---
tags: [type/moc, status/growing]
created: 2025-12-09T21:15:15
updated: 2025-12-26T14:24:38
description: 生活技能、爱好与日常相关知识索引
---

## 日常生活

```dataview
LIST
FROM #life/daily AND -#type/moc
SORT file.name ASC
```

## 人生思考

- [[生命（人活着）的意义]] - 人生意义的思考

## 个人信息

- [[胡航畅]] - 个人账号信息汇总

## 材料

```dataview
LIST
FROM #life/material AND #type/moc 
WHERE file.name != this.file.name
SORT file.mday DESC
```

## 兴趣爱好

```dataview
LIST
FROM #life/hobby AND -#type/moc
SORT file.name ASC
```

## 购物

工厂：

- （溢达纺织-长绒棉厂） （推荐十如仕哟） 2025/1/21 于 bilibili 看到

```dataview
LIST
FROM #life/shopping AND #type/concept 
WHERE file.name != this.file.name
SORT file.mday DESC
```