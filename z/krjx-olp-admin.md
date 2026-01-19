---
tags:
  - tech/dev/frame
  - type/howto
  - status/growing
description: krjx-olp-admin
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[KRJX MOC]] | [[Vue3 MOC]]

---


# krjx-olp-admin 

[olp 项目的 GitLab Address](http://10.248.18.6:9000/olp/phase2/olp-admin)

Vscode 插件：  

- Gitlens
- Less IntelliSense
- Iconify IntelliSense
- i18n Ally
- UnoCSS for VS Code
- PostCSS Language Support
- markdownlint
- DotENV
- YAML
- vscode-icons
- Path Intellisense

## 个人理解

基于一个开源项目：  
> 芋道，以开发者为中心，打造中国第一流的快速开发平台，全部开源，个人与企业可 100% 免费使用。

- 采用 vue-element-plus-admin 实现
- 改换 saas，自动引入等功能
- 使用 Element Plus 免费开源的中后台模版

## 业务

### 考试模板

考试详情页

考试和测验

## 启动 local

直接 pnpm install 失败了，似乎是 vite 版本过低导致，更新后同时更新 nodejs

使用测试环境

```env
# 请求路径
#VITE_BASE_URL=''
VITE_BASE_URL='https://test.portal.v2.olp.credat.com.cn'
```

## 实战

- [[图表导出到excel]]

### 证书名称显示不全

details：  
目标：  有一个框，框内用于显示证书名称（Security Certification）  
错误： 框中只显示 Security，Certification 跑到框外面去了

原因： 样式中使用了 line-height 元素，当文本有空格时，如果文本过长会溢出固定宽度的容器。  

个人理解： 之前的 line-height 就是为了将文字变为块级标签，固定高度，实现自动居中，但有空格导致此问题。  

修改： 使用 flex 布局来实现文字居中

```css
.draggable-item {
  position: absolute;
  width: 120px;
  height: 80px;
  border-radius: 8px;
  user-select: none;
  touch-action: none;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  transition: background-color 0.2s;
  text-align: center;
  // line-height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;

  word-wrap: break-word; // 允许长单词自动换行
  overflow: hidden;
  // white-space: nowrap;
}
```


### 上传取消 BUG

产生原因：  
在 Dialog 关闭时会调用方法来检测是否有文件在上传，有的话就弹窗提醒。  

但是当前的判断逻辑有问题：一旦存在 resource 就认为有文件在传输（实际可能已经传输完成），应该修改判断逻辑，if（传输未结束 && resource）才弹窗。

还有一个问题： 现在是点击 submit 后就直接关闭 Dialog，但是文件仍然在传输。要么到上传成功再关闭弹窗，要么 submit 引发的关闭弹窗不触发 close

解决方案：  
添加 isDialogClosedBySubmit 属性，在提交逻辑中设为 true，关闭弹窗触发的 cancel 逻辑中如果是 isDialogClosedBySubmit 为 true，直接返回。  

### 用户列表渲染

1. 先通过 api 获取已选择用户id 和 当前页面的用户
2. 通过方法来将 已选择用户的样式设为已选中

### 用户列表添加不受控制

现象：  
添加了 400 号用户时，最上面的 121 124 号用户消失了  

原因：  
我发现了它是有一个 selection 对象来在保存已选择的用户id，在点击 add 按钮初始化时，它只能恢复第一页显示的用户id，其他的已选择的用户id 就被丢弃了，除非翻到所在页。

解决方案：  
重要对象：  
所有已经选中的用户id、当前页面的用户id、当前页面选中的用户id
重要方法：  
getList（获取当前页面所有用户）、setTableSelected（设为选中状态）、handleSelection（当表格选中项变化时调用，更新全局选中、当前页面选中等对象）

进入新页面时，筛选出当前页面选中的用户id，调用设为选中样式方法，给表格中的这些用户设为选中样式。  

Selection-change 时：  

- 选择用户时，添加到全局 和 当前页，再调用设为选中样式方法。
- 移除用户时，从全局 和 当前页 中删除

以上两种情况统一为：  
先更新当前页面选中的用户id，然后从全局中去除所有当前页面的用户，再添加当前页面选中的用户id。  

还有一个问题：  
翻页后，触发 getList 来获取新的页面时，应该先 setTableSelected，再 handleSelection，但是翻页会直接让选中项变化触发 handleSelection，导致 setTableSelected 过程中，先触发 handleSelectrion，让当前页选中用户数据丢失。  

解决方法：  
添加 isSettingSelection 标志，为 true 时直接跳过 handleSelection。

还有一个问题：  
当前文件中只传入了 userIds 列表，上传的 api 需要完整的用户数据。当前在选择框页面中要获得完整的用户数据只有当页面到达用户所在页面时触发 page API 获取当前页面所有用户的完整信息。  

解决方法：  
当前是在父组件中传入已选择的用户ID（userId），发现是通过 api 获取了完整的用户数据，但是只传入了 userId，所以只要修改一下，传入需要的完整数据。这样已经选择的用户不需要到所在页面就能有完整数据。  

{
	"code": 0,
	"data": {
		"list": [
			{
				"id": 31296,
				"classId": 9539,
				"nickname": "989",
				"deptName": "989",
				"badgeNo": "989",
				"companyName": "989",
				"positionName": "989",
				"workType": "989",
				"workTerms": "989",
				"className": "",
				"classCode": "",
				"userId": 21820,
				"type": 1,
				"status": 2,
				"statusName": "2",
				"checkInTime": "",
				"checkOutTime": "",
				"attendanceStatus": 1,
				"ddtPermitNo": "",
				"dateOfBirth": "",
				"drivingLicenceNumber": "",
				"issuingDate": "",
				"expiryDate": "",
				"vehicle": "",
				"eyeTest": "",
				"version": 0,
				"createTime": "",
				"attendanceStatusName": ""
			}
		],
		"total": 0
	},
	"msg": ""
}

### el-form 添加字段

1. 添加 refresher 列
2. 让 eyeTest 可直接修改

了解数据的样式

{
    "list": [
        {
            "id": 410,
            "classId": 294,
            "nickname": "LIU LIU",
            "deptName": "Information Management & Information Technology",
            "badgeNo": "9439",
            "companyName": "Antonoil Services DMCC",
            "positionName": "",
            "workType": "0",
            "workTerms": "1",
            "className": "AWS Zure Date Management - 202508251006",
            "classCode": "202508251006",
            "userId": 41,
            "type": 1,
            "status": 1,
            "statusName": "Booked",
            "checkInTime": null,
            "checkOutTime": null,
            "attendanceStatus": 0,
            "ddtPermitNo": null,
            "dateOfBirth": null,
            "drivingLicenceNumber": null,
            "issuingDate": null,
            "expiryDate": null,
            "vehicle": null,
            "eyeTest": null,
            "version": 0,
            "createTime": 1756098106000,
            "attendanceStatusName": null
        }
    ],
    "total": 4
}

### radio

添加一个 other 选项

![alt text](krjx/AAA_PictureDir_krjx-olp-admin/image.png)

{
    "dictType": "training_category_status",
    "value": "1",
    "label": "DDT课程",
    "labelEn": "Is DDT Course",
    "labelCn": "DDT课程",
    "labelAr": "هو دورة DDT",
    "colorType": "",
    "cssClass": ""
}

### 1300 管理端 课程附件自动改名

要实现 content 自动改名成 课程名称 + 01/02/03 的后缀。

appendix 是要废除的，不用管。

开始的 company 列表获取接口：  
https://test.admin.v2.olp.credat.com.cn/admin-api/system/company
确实没有返回 yudaoyuanma 等 company

点击 assign 按钮后调用的获取 company 树形数据的接口：  
https://test.admin.v2.olp.credat.com.cn/admin-api/system/dept/tree?status=0
有返回 yudaoyuanma 等脏数据

应该是后端出了问题

### 打标签

#### Learning Center课程打标

admin-api/learning/course/page 接口获取所有课程

已经有 course Description 和 skill tags 属性和编辑页面

后续操作： 添加 调用 AI 生成的接口按钮（传入 id？）

#### MCL Train 的课程打标

admin-api/academy/course/page 接口获取所有课程（返回内容有id）

有 remark、keywords、keywordsList 属性

后续操作： 添加 调用 AI 生成的接口按钮（传入 id？） 和 skill tags 栏

