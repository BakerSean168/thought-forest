---
tags:
  - tech/dev/project
  - type/journal
  - status/seed
description: OLP项目待研究事项记录
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[项目文档 MOC]] | [[OLP MOC]]

---


```ts
// 删除窗体
delConfirm(content?: string, tip?: string) {
  return ElMessageBox.confirm(
    content ? content : t('common.delMessage'),
    tip ? tip : t('common.confirmTitle'),
    {
      confirmButtonText: t('common.ok'),
      cancelButtonText: t('common.cancel'),
      type: 'warning'
    }
  )
},

const handleDelete = async (id: number) => {
  await message.delConfirm() // 直接使用异步函数来显示组件
  console.log('开始移除课件id', id)
  let resourceIds: string[] = [];
  resourceIds.push(id.toString())
  await ClassInfoApi.deleteMaterial(resourceIds)
  message.success(t('common.delSuccess'))
  await getList()
}
```

