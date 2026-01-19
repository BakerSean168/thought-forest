---
tags:
  - tech/dev/project
  - type/project
  - status/growing
description: krjx-olp-portal
created: 2025-01-01T00:00:00
updated: 2025-12-08T00:00:00
---

> [!info] **上级索引**
> [[KRJX MOC]] | [[Vue3 MOC]]

---


# krjx-olp-portal

## 实战

### 美化页面

1. 让 course 内的卡片的标题只显示一行，多余的内容用 ...，
2. 实现标题悬浮时，弹出 tooltip 展示完整标题 + 详情信息
3. 注意层级，让悬浮框显示在最上面，也不要因为超出 card 而无法显示多余内容

### 经典内容超出容器导致遮挡其他页面

### 给 MCL Training 课程详情页添加样式，完善展示信息

exam 不需要了

- 课程的 remarks 作为 描述
- title 作为 标题

佳齐，我需要实现点击下载 Course 的附件的功能，我觉得需要：  
1. 在 `/academy/course/get?id=` api 中返回 courese 附件的下载地址 或者 资源 id
2. 然后有一个用来调用 地址 或 id 来下载附件的 api

是否已经有可行的方法，还是需要你修改新增一下 api

当前 `/academy/course/get?id=` 返回信息如下
{
    "id": 85,
    "title": "Online acadamy tutorial 02",
    "code": "005",
    "status": 1,
    "categoryId": 37,
    "categoryFullPath": "Soft Skills & Personal Development",
    "examId": null,
    "cover": "/minio-storage/olp-phase2-images/7564d9b7-c81c-4fb1-8a63-04df258e15fe.jpeg",
    "language": 6,
    "languageList": [
        2,
        3
    ],
    "languageStr": [
        "AR",
        "CN"
    ],
    "trainerType": 2,
    "approval": null,
    "approvalConfigId": null,
    "scope": null,
    "validity": 6,
    "feedbackStatus": 1,
    "feedbackLanguage": 3,
    "prerequisteCourse": "",
    "prerequisteAttachment": "",
    "absentTime": 0,
    "freezeTime": 0,
    "certificateId": 6,
    "notification": 0,
    "bookingTime": 0,
    "remarks": null,
    "createTime": 1751437138000
}

my center preview
![alt text](krjx/AAA_PictureDir_krjx-olp-portal/image.png)

### 在 Class Details 中完善信息

`/academy/class-info/my-training` api  

在 Class Details 中添加 考试链接 和 附件下载

api 获取的课程详细信息如下，这个 api 返回的 class 信息也没有 考试、附件相关字段

{
    "code": 0,
    "data": {
        "list": [
            {
                "classId": 295,
                "courseName": "mlcTraining02",
                "validity": 2,
                "categoryName": "Leadership & Management",
                "name": "mlcTraining02 - 202508251019",
                "code": "202508251019",
                "type": 1,
                "trainerId": 39,
                "trainerName": "shiwen01",
                "classRoomId": 21,
                "classRoomName": "MLC-13",
                "scanStartTime": null,
                "checkInTime": null,
                "checkOutTime": null,
                "startDate": "2025-08-26",
                "startTime": "11:20:48",
                "endTime": "12:20:48",
                "trainingDays": 0.5,
                "language": 1,
                "translator": 0,
                "studyStatus": 1,
                "status": 2,
                "publishStatus": 1,
                "maxNum": 12,
                "minNum": 3,
                "liveLink": null,
                "feedbackQrCode": null,
                "description": null,
                "createTime": 1756092060000,
                "assignNum": 2,
                "existBooking": 1,
                "bookingStatus": null
            }
        ],
        "total": 1
    },
    "msg": ""
}

跟 佳齐 沟通后，将 考试Id 和 resourceId 都放在详情接口里  

{
    "code": 0,
    "data": {
        "id": 295,
        "courseId": 91,
        "courseName": "mlcTraining02",
        "courseCover": null,
        "name": "mlcTraining02 - 202508251019",
        "examId": null, ！！！！！
        "code": "202508251019",
        "type": 1,
        "trainerId": 39,
        "trainerName": "shiwen01",
        "classRoomId": 21,
        "classRoomName": "MLC-13",
        "scanStartTime": null,
        "startDate": "2025-08-26",
        "startTime": "11:20:48",
        "endTime": "12:20:48",
        "trainingDays": null,
        "language": 1,
        "translator": 0,
        "status": 3,
        "publishStatus": 1,
        "bookingStartTime": 1756092066000,
        "bookingEndTime": 1756092048000,
        "maxNum": 12,
        "minNum": 3,
        "liveLink": null,
        "feedbackQrCode": null,
        "description": null,
        "createTime": 1756092060000,
        "assignNum": null,
        "assignmentType": null,
        "resourcesList": [
            {
                "id": 2315,
                "identity": "L_202508260002",
                "mediaType": 3,
                "lang": "1",
                "fileId": 3609,
                "address": null,
                "title": "新建 DOCX 文档.pdf",
                "format": null,
                "type": "L",
                "size": 22907,
                "duration": 300,
                "subjectId": null,
                "deptId": 26,
                "scormParseStatus": null,
                "scormVersion": null,
                "scormRunPath": null,
                "aiccParseStatus": null,
                "referenceCount": 0,
                "enable": "1",
                "createTime": 1756189192000,
                "depts": null,
                "deptIds": null
            }
        ]
    },
    "msg": ""
}

那我就需要 根据 考试Id 跳转到 考试页面的 api，或者 直接拼接考试Id 跳转页面：  
1. 在 ClassDetails.vue 中，已经有粗略的 class 信息，我需要再次调用

### MLC Training Course 卡片显示优化

{
    "id": 95,
    "title": "AWS Zure Date Management",
    "code": "003",
    "status": 1,
    "categoryId": 48,
    "categoryFullPath": "DDT-test",
    "examId": 466,
    "cover": "https://resource.credat.com.cn/sas/018701a48ca166e619f43daff08b925839bd43988213e58ffafd77fcbddd24a7.jpg",
    "language": 4,
    "languageList": [
        3
    ],
    "languageStr": [
        "CN"
    ],
    "trainerType": 3,
    "approval": null,
    "approvalConfigId": null,
    "scope": null,
    "validity": 1,
    "feedbackStatus": 0,
    "feedbackLanguage": 1,
    "prerequisteCourse": "91",
    "prerequisteAttachment": "",
    "absentTime": 0,
    "freezeTime": 0,
    "certificateId": 11,
    "notification": 0,
    "bookingTime": 0,
    "remarks": "<span>This 15-video course explores such topics as Azure Active Directory Domain Services AD DS) and access management features in Microsoft 365. Modern businesses need modern security and safe access for users that could be easily managed by administrators, and Microsoft 365 provides those features. Key topics covered in the course include directory structures and their role in network and organizational environments; trust as the security principle defining information and access safety, established by a directory structure managed and maintained by network administrators and delivered to end users. Examine the concepts of protection and domain controllers. Learn about Azure AD Connect, pass-through authentication, Azure AD Seamless Single Sign-On, and federation using Azure AD. A closing exercise asks learners to describe features of AD DS; list examples of AD DS objects and three AD DS Forest models; list four editions of Azure AD; and list the steps for enabling password hash synchronization. The course can be used as part of the preparation for the Microsoft 365 Fundamentals (MS-900) exam.</span>",
    "createTime": 1755608848000
}

{
    "id": 90,
    "title": "mlcTraining01",
    "code": "004",
    "status": 1,
    "categoryId": 36,
    "categoryFullPath": "Leadership & Management-Leadership Development Program powered by MIT SMR",
    "examId": null,
    "cover": null,
    "language": 1,
    "languageList": [
        1
    ],
    "languageStr": [
        "EN"
    ],
    "trainerType": 4,
    "approval": null,
    "approvalConfigId": null,
    "scope": null,
    "validity": 10,
    "feedbackStatus": 0,
    "feedbackLanguage": 1,
    "prerequisteCourse": "",
    "prerequisteAttachment": "",
    "absentTime": 1,
    "freezeTime": 1,
    "certificateId": 5,
    "notification": 0,
    "bookingTime": 0,
    "remarks": null,
    "createTime": 1752128715000
}

### Home 页面的 todo 列表展示

Live Streams 的 todo 表中要展示符合要求的数据
确认了一下，数据没有问题。  
给 mandatory course 添加了 badge。  

### my development plan 中添加 必修课标识

目标：  
添加标识  
    进入 Study Plan页面，课程清单中，如果某个课程是从 Content Course 里指派的必修课，需要有个Tag标记为必修课（Mandatory Course）。

`/app-api/edp/study-plan/page`
api返回数据
{
    "id": 20,
    "positionId": 636,
    "phase": 1,
    "skillId": 323,
    "skillName": "Information Security",
    "firstLevelSkillName": "Hard Skill",
    "secondLevelSkillName": "Domain-Specific Skills",
    "progress": 2000,
    "status": 2,
    "startDate": "2025-06-10",
    "endDate": "2025-07-11",
    "studyPlanContentList": [
        {
            "planContentId": 20,
            "recommendId": 41,
            "bizType": 10,
            "matchDegree": 85,
            "level": 1,
            "bizId": 2901,
            "title": "AWS Cloud Practitioner 2019: AWS Access Management",
            "skillId": 323,
            "planId": 20,
            "status": 2,
            "progress": 100,
            "skipped": true,
            "skipReason": "不想学"
        },

    ]
}

似乎没有返回一个字段来让我判断 课程是不是从 Content Course 里指派的必修课  

晓琪，你那里能不能返回一个相应的字段，还是说已经有实现的方法了

### 给 列表增加字段

{
    "deptId": null,
    "compId": 119,
    "sectId": null,
    "sectName": null
}

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

### 修复页面的字段展示（中文改英文）

### Learning Center-Company Policy 中的卡片的时间字段未展示数据

{
    "id": 41,
    "departmentId": 26,
    "departmentName": "Information Management & Information Technology",
    "title": "Cp-08-15-01",
    "cover": "https://resource.credat.com.cn/sas/c30b6fee3b16871791b97d970027064422ae0316f763d2b3bbd44bc88f82fc72.png",
    "declaration": null,
    "content": null,
    "keywords": "",
    "origin": 1,
    "format": "video/mp4",
    "mediaType": 1,
    "lang": "1",
    "duration": 33,
    "durationLower": null,
    "durationUpper": null,
    "ack": false,
    "sort": 1,
    "attachmentList": null,
    "fileTypeList": [
        "video/mp4"
    ],
    "attachmentNames": null,
    "createTime": 1755248316000,
    "updateTime": 1755248316000,
    "creator": "117",
    "createBy": null,
    "creationTime": null,
    "updater": 117
}

发现是因为 duration数据处理函数里的逻辑直接把 秒级 的数据丢失了，导致只能显示 1m（好像是四舍五入，那就<1m） 以上的数据  

解决  
修改函数逻辑，支持秒级数据展示。  

### EDP-管理端-CDP（职业晋升路线）- 添加岗位下拉框内容显示优化

目标  
下拉框里的内容（可选择的岗位），打个Tag标记（标记部门名称+岗位编码）

当前  
api `edp/career-develop/get-positions-for-new-path` 返回数据（id + 职位名称）：  
 "data": [
    {
        "id": 3916,
        "name": "Data Center Manager"
    },
    {
        "id": 3917,
        "name": "Data Center Operation and Maintenance Manager"
    },
]

需要  
部门名称：
岗位编码：是 id 吗

晓琪，你那里能不能在 api 中直接返回 部门名称 和 岗位编码


### 推荐学习内容表格串行

{
    "bizType": 10,
    "bizId": 2594,
    "level": null,
    "title": "SNCF: Dashboards, Reporting, Troubleshooting, Packet Capture, & Cisco AMP",
    "keywords": [
        "Reporting, Troubleshooting, Security, Dashboard Management, Cisco UCS, Cisco Firepower"
    ],
    "skills": {
        "1": {
            "4": [
                {
                    "id": 3218,
                    "name": "Troubleshooting",
                    "matchDegree": 89,
                    "matchReason": "The course focuses on troubleshooting packet drops and capturing packets, which are essential troubleshooting tasks."
                }
            ]
        },
        "2": {
            "7": [
                {
                    "id": 3217,
                    "name": "Reporting",
                    "matchDegree": 92,
                    "matchReason": "The learning content explicitly mentions the generation and management of reports using Firepower system."
                }
            ]
        }
    }
}
{
    "id": "skill_2594_2_7_3217",
    "bizType": 10,
    "bizId": 2594,
    "title": "SNCF: Dashboards, Reporting, Troubleshooting, Packet Capture, & Cisco AMP",
    "keywords": [
        "Reporting, Troubleshooting, Security, Dashboard Management, Cisco UCS, Cisco Firepower"
    ],
    "level": null,
    "introduction": "",
    "firstName": "Hard Skill",
    "secondName": "",
    "skillName": "Reporting",
    "matchDegree": 92,
    "matchReason": "The learning content explicitly mentions the generation and management of reports using Firepower system.",
    "firstSkillId": "2",
    "secondSkillId": "7",
    "thirdSkillId": 3217
}
## 研究

SuperSearch 组件 是怎么复用的
