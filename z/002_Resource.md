---
created: 2025-12-09T21:12:26
updated: 2025-12-10T17:12:35
tags: [type/moc, status/growing]
description: 记录遇到的好用的工具、有用的资源
---


该页面用于分类索引 resource，使用 dataview

现根据主领域标签 `#type/resource` 确定，
再用 其他次要领域标签确定类型 `#game`、`#book`、`#video`

[[资源与资源获取]]
## workflow

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #tech/dev/workflow    
```

## transport

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #transport   
```

## device

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #device   
```


## note

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #note   
```

## download

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #download   
```


## 设计

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #design  
```

## 流程图

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #picture 
```

## browser

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #browser
```

## cloudstorage

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #cloudstorage
```

## game

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #game
```

## compress

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #compress
```

## 视频

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #video
```

## Cybersec

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #tech/security 
```

## ops

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #tech/ops 
```

## Vscode插件

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource AND #tech/app/vscode AND #extension  
```


## ALL

```dataview
TABLE WITHOUT ID 
file.link AS "入口", description AS "说明"
FROM #type/resource
```