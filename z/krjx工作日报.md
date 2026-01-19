---
tags:
  - life/work
  - type/howto
  - status/growing
description: krjx工作日报
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[职业发展 MOC]] | [[生活 MOC]]

---


## 20250821

1. 入职填表
2. 企业微信、钉钉、邮箱
3. OpenVPN、GitLab
4. 克隆项目代码
  [项目地址](http://10.248.18.6:9000/olp/phase2/olp-admin)  
  直接使用 ssh 克隆失败，要使用 http 链接  
  项目使用插件：Less IntelliSense
5. 下载 Figma
6. 处理BUG
7. 提价代码

## 20250822

早会：  

1. 详情页面需要完成
2. exam 模板问题，exam（考试） 和 test（测验）的业务逻辑
  公司是怎么思考这些功能的，类似产品（学习通）中已经有测验和考试的功能。  
3. 客户觉得有些地方莫名奇妙不好用。

## 20250829

1. cancel class时候增加取消原因的输入框 / 前端
  使用了 el-input 组件，并添加文本域标签
2. 新增课程分类里，删除is DTT 和 Is HSE的选择框
  删除了上面的复选组件，并利用父节点选择框中选择的父节点和选择框选项的树结构来获取根节点，并根据根节点的属性来判断添加的分类的类型；优化：可以直接根据父节点的类型来获取类型。
3. Learning Center-页面提示优化
  去除了不合理的逻辑判断
4. Online academy classroon里面location可手动添加地址 / 前端
  新增了 location Dialog 组件 和 + location 按钮；在 academy 接口文件中新增教室相关接口；修改 location 下拉框中的选项获取，不再通过字典获取，而是通过接口来获取； 总结：提升了 location 的灵活性

## 20250901

1. 重新上传 LocationDialog 组件，
  vscode 中有该文件变化的追踪，但是 webStorm 中没有追踪该文件
2. 重新修改部门下拉框的 tag 标签内容。只需要子部门和岗位编码
3. 管理端登录界面优化
  去除多余按钮，去除记住密码（待：是不要默认记住密码，还是去除这个功能）
4. 用户端home页中的MLC Training模块，点击具体课堂后，也跳转至my center-my trainings页面，应该是要跳转到具体的详情页面。
  修改router配置，使用正确的path和参数
5. 点击 add to waitinglist 后无法选择日期
  但是我在测试时（本地的测试环境和带域名的纯测试环境）是没有问题的
6. Online Academy-Class Schedule-课堂管理功能
  也是之前我就测试没有的问题的单子

## 20250902

### 修改记住密码相关问题

配置环境变量，在 dev 环境下读取环境中配置的账号密码，生产环境下为空（可以读取缓存）

### 2. 下拉岗位列表

1. 前端应该是已经实现了 code（岗位编码） 和 sectName（子部门） 属性以 tag 标签形式显示的功能，根据返回的数据可以看出，有些返回的数据是没有code属性或者 sectName 的数据的（code：null），所以不会显示；
2. 下面的 + 按钮调用的也是相同的对话框，已经是有这个效果的。

关键：  
1. 这些岗位的数据是否正确，是不是应该在后端的数据库中先添加完整的数据

### 3. 不再使用封装的 DatePicker 组件，而是使用 Calendar 组件，自己拼接#### 4. 修改界面字段

### 5. Learning Center-页面提示优化

去除了不合理的逻辑判断

## 20250903

### 1. `#1533EDP- JD Generator（Chat页面）- 增加部门、子部门、、编码）`

需要 deptName、sectName、positionName、uniqueCode（子部门编码）

上次新实现的接口`/edp/jd-version/post-list`

 {
	"id": 1,
	"name": "Managing Director",
	"code": "AOSIMGMTSECPOS001",
	"sort": 0,
	"status": 0,
	"compId": 1,
	"companyName": null,
	"comShortname": null,
	"deptId": 8,
	"deptName": null,
	"depShortname": null,
	"sectId": null,
	"sectName": null,
	"shortName": null,
	"parentId": 0,
	"ancestors": null,
	"dataSource": null,
	"jdPublished": true
},

旧接口`/system/post` api 返回内容：  

{
	"id": 1,
	"name": "Managing Director",
	"code": "AOSIMGMTSECPOS001",
	"sort": 0,
	"status": 0,
	"remark": null,
	"createTime": 1728381782000,
	"compId": 1,
	"companyName": "Antonoil Services DMCC",
	"comShortname": "AS",
	"deptId": 8,
	"deptName": "Management",
	"depShortname": "MGMT",
	"sectId": null,
	"sectName": null,
	"section": null,
	"shortName": null,
	"parentId": 0,
	"parent": null,
	"ancestors": "",
	"dataSource": "MDS",
	"children": []
}

1. 显示完整职位路径（deptName、sectName、positionName、code）
2. 单行显示，内容超出则用省略号
3. 鼠标悬停在对应层级，能出现悬浮框显示完整信息

### 2. `#1256EDP-课程和技能匹配手动管理运营模块`

1、搜索增加：按技能精确搜索，按Title模糊搜索，按匹配度范围精确搜索。按Keywords搜索。
2、功能优化：
1）匹配推荐过的显示出来后。需支持可以手工点击Match按钮重新匹配，AI得出一个匹配度和匹配原因，由人来点击一个Confrim按钮确认是否更新系统自动匹配的结果。
2）未被推荐和匹配过的需要增加一个按钮，弹一个页面，将未匹配过的课程展示出来，然后调用AI接口去手工一个一个Match匹配。手工匹配操作同上。

`/edp/learning-content-recommend/page`  
这个 api 请求参数有 title、keywords、level 应该是能支持 Title模糊搜索、Keywords搜索；

但是还需要  
- 按技能搜索（应该是Hard Skill、Cybersecurity Threats and Mitigation 这种，而非难度等级）
- 匹配度范围搜索
- 返回 未被推荐和匹配过的课程

1. 添加了 展示未匹配内容的按钮和对话框（使用下拉列表来筛选不同类型的内容）
2. 调用 api 实现各种筛选

### 3. 重新给表格添加字段

上次被回滚了  
这次使用新 api 的 jdPublished 字段 + 老 api 的其他字段，新 api 的部分字段为 null，但老 api 中存在。  

### 4. 去除 status 的 badge 标签

### 5. 修改 jd 表格的 status 字段逻辑，并删除 creator 字段

### 6. 导航栏加宽到 300 px

## 20250904

### fix(category): 修复了 category 的 training 页面的一些 bug

1. 添加课程分类表单中增加 Other Course 选项（字典中添加）
2. 在 edit 时能正确显示 status 状态
补充：查看字典 key: HSE 对应 0，DDT 为 1， Other 为 2；后端返回数据 为 1、2、3。

总结：  
1. 现在用户在 traing 页面添加新的课程时，能够自己选择 DDT、HSE、Ohter 三种类型。
2. 并且 编辑 时的表单也能正确反映当前课程的类型。  

### EDP-课程和技能匹配手动管理运营模块前端功能实现

1. 根据联调文档来调整参数和使用接口
2. 创建新的页面组件

总结：  
1. 用户可以用以下方式搜索推荐学习内容：按技能精确搜索，按Title模糊搜索，按匹配度范围精确搜索。按Keywords搜索
2. 用户可以查看未被匹配过的学习内容，并手动匹配
3. 用户可以重新匹配已匹配过的学习内容

### 用户端 exam todo 列表

负载
pageNo 1
pageSize 3
status 1
type 0

响应为空

问题：  
type 为 1 是返回代办；为 0 是返回历史数据；

### 用户端 todo 总数

总结：  
现在能够正确显示所有 todo 总合  

### live todo 没有展示自己创建的 live

`/app-api/live/room/participated-page`  
查询我参与的直播房间信息的接口

`live/room/publish`  
自己创建 live 的接口

1. 创建 live 时，my center中的 my live streams 的内容（调用`/app-api/live/room/participated-page`）没有增加。说明后端没有正确把 自己创建的 live 和 自己参与的 live 关联

解决：  
让后端将自己创建的 live 与 自己参与的 live 关联

### JD Generate 增加岗位信息

现在用户能够看到生成岗位信息的完整路径

## 20250905

1. 现在点击 todo 的具体 survey 能直接跳转到该 survey 的 detail 页面
2. 现在通过点击 all topics 进入一级目录时，或显示返回按钮；用户可以通过返回按钮来便捷地返回
3. 修复了代码修改导致的 chatbot 不显示对话记录的错误，现在能正确显示完整的对话记录了

## 20250907

1. 初步创建了用户端的知识生成页面和知识管理页面
2. 优化文档管理、文档生成页面，新增复制、预览、发布功能页面
3. 给管理端知识文档页面添加按照文档类型筛选的功能，现在用户能筛选生成的文档和上传的文档

## 20250909

1. 用户端知识生成模块继续优化界面细节，添加输入框上的提示词和下方的上传按钮
2. 用户端知识生成模块开始尝试接入api接口，获取聊天记录、获取知识文档列表的接口可以工作了，但是创建新会话、保存知识文档、请求发送知识文档和聊天生成回答的接口都还有问题。
3. 用户端知识文档管理也接入了 api，但没有真实数据，不知道是否正确
4. 管理端开始尝试复用 chatbot 处的代码来实现知识生成页面，但是遇到了动态路由配置报没有子组件的错误，正在问明超。
5. 管理端文档管理页面初步实现
6. 修改了用户端exam todolist 中 exam 状态的获取逻辑，现在能正确 exam 的状态了

## 20250910

1. JD聊天头部岗位路径样式优化，现在更符合用户的审美了吧
2. 登录框样式优化
3. 初步复用了 chatbot 的聊天页面
4. 用户端 Home 页面的任务完成率
  需求：完成一个任务后更新任务完成度  
  当前 `/learning/home/statistics` 获取统计信息的 api 只返回了 学习时间，是不是还应该返回任务完成度
5. 管理端课堂附件下载不显示问题
  修改了使用的接口，现在应该能正确显示资源了

## 20250911

1. 用户端-My Center-Company Policy模块-状态展示优化
2. 管理端开始编写知识文档相关接口的类型并调用
3. 管理端增加拒绝发布、发布等对话框组件
4. 用户端开始编写知识文档相关接口的类型并调用
5. 用户端修复一些业务逻辑，现在用户能直接下载生成页面的文档，不需要申请

## 20250912

1. 继续应用接口
2. 协调测试门禁机器
3. 沟通AI相关接口的实现和调用

## 20250915

1. 在管理端用户后方显示用户角色，现在用户可以方便地看到自己的角色
2. 在管理端用户信息页面新增字段，现在用户可以看到更多的相关信息
3. 实现了用户端Home页面的todo表能够点击后直接跳转到对应数据的详情页面，除了 certificates，因为 它没有详情页，是图片，所以仍然跳转到中心页。
4. 查找管理端 company 列表显示问题的发生原因

## 20250916

1. 用户端移除首页的完成率展示
2. 实现课程添加的附件的名称自动复习为课程名称
3. 用户端修改个人中心部分标签内容，修改 MLC Training 中的类别；并实现之前未实现（只有前端样式，没有传参）的筛选功能
4. 管理端和用户端界面优化

## 20250917

1. class schedule 里面manage里的roster界面4个修改，现在用户能用下拉框来修改对应属性
2. 在考试结果页面添加返回按钮，用户点击后会跳转到 mycenter 的考试中心页面
3. 用户端和管理端知识文档生成页面的聊天输入框样式修改
4. 知识生成回答内容的渲染测试

## 20250918

1. 去除掉登录页面的 AD login 选项
2. 修复了 edit 弹窗中没有正确获取 location 的问题
3. 测试知识文档接口

## 20250919

1. EDP-Recommended Learning Content 重新手工匹配功能完善
2. 初步实现了知识生成大纲；
3. 优化了个人知识管理页面和部门知识管理页面的样式风格，与其他页面相统一
4. 根据新的要求，重新修改知识文档相关的接口的调用和传参
5. mycenter 页面的标题修改（Course Test 修改为 Course Final Quiz）
6. 修复了除了 pdf 以外类型的附件下载失败的问题
7. mlc training 页面优化，添加无数据提示
8. Training Course新增或编辑考试数据优化
9. 去除了 Class Schedule 中点击新增时多余的提示，优化用户体验
10. 管理端和用户端登录页添加提示

## 20250921

1. My Certificates证书数据优化
2. mlc training item 跳转问题
3. my center/ mlc training 页面中的课程内容显示优化，去除未发布课程
4. 修复了 证书 不能正确预览的问题
5. 管理端知识文档页面新增批量操作功能

## 20250922

用户端没有获取 location 的接口

1. 跑通知识生成接口，现在能正确生成和显示知识文档
2. 跑通保存、更新知识文档接口，现在能保存和更新知识文档
3. 使用批量提交、批量删除接口，现在能在知识文档管理页面批量操作知识文档

## 20250923

1. 管理端和用户端登录页面优化
2. 用户端 my exam 页面返回按钮改为面包屑
3. 知识文档筛选接口，现在支持状态查询，时间查询，标题模糊查询以及前端 类型（生成、上传）查询
4. 部门知识库的获取、展示、生成文档的预览、下载
5. 管理端知识管理的上传、生成文档的预览、批量拒绝、批量删除

## 20250924

1. 登录框 label 移除（注释）
2. 错误提示词优化
3. 管理端知识文档的标签和 summary 生成
4. 用户端聊天会话标题修改
5. 用户端和管理端的文档预览页面实现
6. 文档下载优化

## 20250925

1. Learning Center课程打标和MCL Train 的课程打标分析；
2. 用户端知识生成文档的会话标题修改接口修复
3. 海外培训内容详情展示

## 20250926

1. 优化用户端知识生成聊天框的初始提示语
2. 探查 Personal员工管理功能-角色显示异常 问题的原因
3. 探查  管理端-公司管理-Assign指派Contract Holder 问题的原因
4. 优化管理端 登录框 的样式，调整外边距和间距

## 20250927

匹配技能默认移除
![[Pasted image 20250929163250.png]]status=3 ： 课程被删了

### 20251110

#### 1851 My Center-My MLC Training-Training Programs-list页面数据展示、搜索、加入课堂、考试、反馈
[BUG #1851 My Center-My MLC Training-Training Programs-list页面数据展示、搜索、加入课堂、考试、反馈 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1851.html)

问题判断：
应该是国际化出了问题；（mycenter -> myCenter)
时间显示出了问题；(字段名字错了)

#### [BUG #1851 My Center-My MLC Training-Training Programs-list页面数据展示、搜索、加入课堂、考试、反馈 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1851.html)

**日期筛选功能异常**

能够正确返回课程的开始日期在 筛选的startDate 之后的课程，但是 **筛选的 endDate 没有生效**，应该是要（在有明确 endDate的时候）能够返回的课程的结束日期在 endDate 之前吧


**筛选 2025-11-04 到 2025-11-07**
**结果返回内容仍然有 2025-11-10 - 2025-12-16**

（个人认为）返回的课程应该是要在 2025-11-04 后开始 2025-11-07 前结束的；
```json
// 请求接口  
https://test.portal.v2.olp.credat.com.cn/app-api/academy/dispatch/page?pageNo=1&pageSize=12&startDate=2025-11-04&endDate=2025-11-07

// 负载
pageNo
1
pageSize
12
startDate
2025-11-04
endDate
2025-11-07

// 响应
{
    "code": 0,
    "data": {
        "list": [
            {
                "id": 1398,
                "code": "MJN-INT-1394",
                "title": "123123",
                "titleAr": "123123",
                "type": 2,
                "dispatchLocationId": 12,
                "dispatchLocationName": "Dubai",
                "startDate": "2025-11-10",
                "endDate": "2025-12-16",
                "duration": 37,
                "implementingCompany": "123",
                "attachments": "[]",
                "remark": null,
                "createTime": 1762747982000,
                "userCount": 1,
                "trainer": null,
                "contractNo": null,
                "timeText": "2025-11-10 - 2025-12-16",
                "travelRequired": 0,
                "travelDate": null,
                "returnDate": null,
                "receivingPlace": null,
                "comments": null
            }
        ],
        "total": 1
    },
    "msg": ""
}
```

有无附件字段 和 时间字段 显示有误
都是处理问题

传参有问题，应该是 数组形式传参，同时传入两个

#### [BUG #1824 Online Academy-HSE & DDT Training-Waitinglist-list页面查看、筛选、重置、新增功能 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1824.html)

添加 location 字段的时候，发现后端返回的 locationId 一直为 null

![[Pasted image 20251110162610.png]]
#### [BUG #1822 Online Academy-Class Schedule-课堂管理功能-材料上传筛选 - 在线学习平台二期（OLP） - 禅道](http://111.196.180.221:8087/zentao/bug-view-1822.html)

初始测试没有问题，
使用 回车 筛选，页面刷新，失去 classId，返回后恢复，但是此时筛选出现数据查不到问题。
查看网络请求发现：
- 后续请求传参中少了 classId、title
- 点击 reset，后续传参会多出 resourceId


### 20251120

`app-api/academy/class-booking/page` 获取预定的 training

### 20251202

- ddcc 签名打印功能测试+优化
	- 和测试人员确认打印组件是否所有地方都已替换
	- 修复按钮多显示的错误
	- 修改打印 preview 页面的中文为英文
	- 替换原本的打印组件
- bbb 字幕样式优化实现中
	- 打算新建一个代码仓库，确保项目代码清晰
		- 获取源码
	- 应用之前修改的代码（在公司服务器）到新仓库
		- 服务器性能差，开发工具打开卡慢信息不加载
		- 尝试传回本地开发，网速慢
