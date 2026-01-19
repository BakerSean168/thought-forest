---
tags:
  - tech/lang/javascript
  - type/concept
  - status/growing
description: 浏览器window全局对象详解
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# window对象 

## 基础概念

浏览器环境中的全局对象，内置了许多方法，如`alert()`,`serTimeout()`，还提供了提供浏览器操作能力（地址栏、历史记录、屏幕信息等）。

## 使用指南

### `window.open()`

| 参数值| 效果| 适用场景|
|------------|-------------------------------|------------------------------|
| `_blank`| **强制新标签页**| 需要保留原页面时（如跳转后台）|
| `_self`| 当前标签页打开（默认行为）| 普通页面跳转|
| `_parent`| 在父级框架中打开| 嵌套 iframe 场景|
| `_top`| 在整个窗口顶部打开| 需要退出框架集时|

```javascript
window.open(
'https://test.admin.v2.olp.credat.com.cn/',
'_blank',
'width=800,height=600,top=100,left=100'
);
```

## 实战经验

## 经验总结

## 信息参考
