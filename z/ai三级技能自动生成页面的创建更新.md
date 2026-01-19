---
tags:
  - life/work
  - type/howto
  - status/growing
description: ai三级技能自动生成页面的创建更新
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[职业发展 MOC]] | [[生活 MOC]]

---


# ai三级技能自动生成页面的创建更新 

## 问题详情

首先有三个一级技能，id 为 1-3，
然后每个技能有可配置（增加）的二级技能，id 为 4-x。
然后调用 ai 来自动生成每个二级技能的下属三级技能，以 json 格式返回（包含一级技能、二级技能，然后前端再将对应三级技能渲染到）

## 解决方案

### 第一步

后端提供一个接口（传入 positionId,返回配置好的二级技能）

```json
这是配置树的结构：

{
  "code": 0,
  "data": [
    {
      "level": 1,
      "id": 1,
      "parentId": 0,
      "name": "Soft Skill",
      "children": [
        {
          "level": 2,
          "id": 4,
          "parentId": 1,
          "name": "Problem Solving & Critical Thinking",
          "children": []
        },
        {
          "level": 2,
          "id": 5,
          "parentId": 1,
          "name": "Communication & Interpersonal Skills",
          "children": []
        },
        {
          "level": 2,
          "id": 6,
          "parentId": 1,
          "name": "Leadership & Management",
          "children": []
        }
      ]
    },
    {
      "level": 1,
      "id": 2,
      "parentId": 0,
      "name": "Hard Skill",
      "children": [
        {
          "level": 2,
          "id": 7,
          "parentId": 2,
          "name": "Industry-Specific Skills",
          "children": []
        },
        {
          "level": 2,
          "id": 8,
          "parentId": 2,
          "name": "Domain-Specific Skills",
          "children": []
        }
      ]
    },
    {
      "level": 1,
      "id": 3,
      "parentId": 0,
      "name": "HSE",
      "children": [
        {
          "level": 2,
          "id": 9,
          "parentId": 3,
          "name": "Health Safety & Security Environment",
          "children": []
        }
      ]
    }
  ],
  "msg": ""
}
```

此时前端已经可以渲染 一级技能+二级技能的框架，并且三级技能可以让用户手动生成

然后点击 ai生成技能的按钮，获取相应并处理渲染。

`data:{"code": 0, "message": "", "data": {"answer": "# Hard Skill\n\n## Industry-Specific Skills\n- Talent Acquisition\n- Performance Management\n- Employee Relations\n- Training & Development\n- Compensation & Benefits Administration\n- HR Data Analysis\n- Policy Development\n- HRIS Systems\n- IT Recruitment\n- Technical Talent Sourcing\n- IT Performance Review\n- IT Training Needs Analysis\n- IT Compensation Benchmarking\n- IT Policy Implementation\n- Data Analysis for HR\n- Workforce Planning\n- Succession Planning\n- HR Metrics & Reporting\n\n## Domain-Specific Skills\n- Human Resource Management\n- Labor Law Compliance\n- Data Science (for HR Analytics)\n- Statistical Analysis\n- Computer Science (understanding IT roles)\n- Economics (compensation analysis)\n- Quantitative Analysis\n- Microsoft Office Suite\n- Reporting & Dashboarding\n- Data Interpretation\n- Predictive Modeling (HR)\n- HR Technology Implementation\n- Change Management (HR initiatives)\n- Organizational Development\n- Employee Lifecycle Management\n\n---\n\n```json\n{\n  \"Hard Skill\": {\n    \"Industry-Specific Skills\": [\n      \"Talent Acquisition\",\n      \"Performance Management\",\n      \"Employee Relations\",\n      \"Training & Development\",\n      \"Compensation & Benefits Administration\",\n      \"HR Data Analysis\",\n      \"Policy Development\",\n      \"HRIS Systems\",\n      \"IT Recruitment\",\n      \"Technical Talent Sourcing\",\n      \"IT Performance Review\",\n      \"IT Training Needs Analysis\",\n      \"IT Compensation Benchmarking\",\n      \"IT Policy Implementation\",\n      \"Data Analysis for HR\",\n      \"Workforce Planning\",\n      \"Succession Planning\",\n      \"HR Metrics & Reporting\"\n    ],\n    \"Domain-Specific Skills\": [\n      \"Human Resource Management\",\n      \"Labor Law Compliance\",\n      \"Data Science (for HR Analytics)\",\n      \"Statistical Analysis\",\n      \"Computer Science (understanding IT roles)\",\n      \"Economics (compensation analysis)\",\n      \"Quantitative Analysis\",\n      \"Microsoft Office Suite\",\n      \"Reporting & Dashboarding\",\n      \"Data Interpretation\",\n      \"Predictive Modeling (HR)\",\n      \"HR Technology Implementation\",\n      \"Change Management (HR initiatives)\",\n      \"Organizational Development\",\n      \"Employee Lifecycle Management\"\n    ]\n  }\n}\n```", "reference": {}, "param": [{"key": "job_title", "name": "job_title", "optional": false, "type": "line", "value": "Deputy HR Manager"}, {"key": "job_description", "name": "job_description", "optional": false, "type": "line", "value": "**Deputy HR Manager**\n\n**Department:** Information Technology **Reporting Line:** IM Manager\n\n**Job Purpose**\n\nTo support the development and implementation of HR strategies and initiatives aligned with the organization’s goals, specifically within the Information Technology department. This role focuses on attracting, developing, and retaining top talent to drive innovation and achieve business objectives.\n\n**Key Responsibilities**\n\n-   **Talent Acquisition:** Manage the full recruitment lifecycle for IT positions, including job posting, screening resumes, conducting interviews, and extending offers.\n    \n-   **Performance Management:** Facilitate the performance review process for IT employees, providing guidance to managers and employees on goal setting, feedback, and development planning.\n    \n-   **Employee Relations:** Address employee relations issues within the IT department, ensuring fair and consistent application of company policies and procedures.\n    \n-   **Training and Development:** Identify training needs within the IT department and coordinate training programs to enhance employee skills and knowledge.\n    \n-   **Compensation and Benefits:** Administer compensation and benefits programs for IT employees, ensuring compliance with company policies and legal regulations.\n    \n-   **HR Data Analysis:** Collect and analyze HR data to identify trends and insights, and make recommendations for improving HR programs and initiatives.\n    \n-   **Policy Development:** Assist in the development and implementation of HR policies and procedures.\n    \n-   **Employee Engagement:** Implement initiatives to promote employee engagement and create a positive work environment.\n    \n-   **Compliance:** Ensure compliance with all applicable labor laws and regulations.\n    \n\n**Required Qualifications**\n\n-   Master’s degree or PhD in Data Science, Statistics, Computer Science, Economics, or a related quantitative field.\n    \n-   Proven experience in a human resources role, with a focus on talent management and employee relations.\n    \n-   Strong understanding of HR principles, practices, and legal regulations.\n    \n-   Excellent communication, interpersonal, and problem-solving skills.\n    \n-   Ability to work independently and as part of a team.\n    \n-   Proficiency in Microsoft Office Suite and HRIS systems.\n    \n-   Experience working in a fast-paced, dynamic environment.\n    \n-   Strong analytical and data interpretation skills."}, {"key": "position_tags_last", "name": "position_tags_last", "optional": true, "type": "line"}, {"key": "skill_library", "name": "skill_library", "optional": true, "type": "line", "value": ["Health and Safety (Office Environment)", "Company HSE Policy", "Safe Work Practices", "English Language Skills", "H2S Awareness"]}, {"key": "skill_classification_command_template", "name": "skill_classification_command_template", "optional": true, "type": "line", "value": "一级分类: Hard Skill\n  二级分类: Industry-Specific Skills\n  二级分类: Domain-Specific Skills\n一级分类: HSE\n  二级分类: Health Safety & Security Environment\n一级分类: Soft Skill\n  二级分类: Problem Solving & Critical Thinking\n  二级分类: Communication & Interpersonal Skills\n  二级分类: Leadership & Management"}, {"key": "skill_template_markdown", "name": "skill_template_markdown", "optional": true, "type": "line", "value": "# Hard Skill\n## Industry-Specific Skills\n## Domain-Specific Skills\n... (No more than 20 tags, one per line, starting with \"- \") \n\n---\n# HSE\n## Health Safety & Security Environment\n... (No more than 20 tags, one per line, starting with \"- \") \n\n---\n# Soft Skill\n## Problem Solving & Critical Thinking\n## Communication & Interpersonal Skills\n## Leadership & Management\n... (No more than 20 tags, one per line, starting with \"- \") "}, {"key": "skill_template_json", "name": "skill_template_json", "optional": true, "type": "line", "value": "{\n  \"Hard Skill\":   {\n    \"Industry-Specific Skills\": [\"Industry-Specific Skills 1\", \"Industry-Specific Skills 2\"],\n    \"Domain-Specific Skills\": [\"Domain-Specific Skills 1\", \"Domain-Specific Skills 2\"]\n  },\n  \"HSE\":   {\n    \"Health Safety & Security Environment\": [\"Health Safety & Security Environment 1\", \"Health Safety & Security Environment 2\"]\n  },\n  \"Soft Skill\":   {\n    \"Problem Solving & Critical Thinking\": [\"Problem Solving & Critical Thinking 1\", \"Problem Solving & Critical Thinking 2\"],\n    \"Communication & Interpersonal Skills\": [\"Communication & Interpersonal Skills 1\", \"Communication & Interpersonal Skills 2\"],\n    \"Leadership & Management\": [\"Leadership & Management 1\", \"Leadership & Management 2\"]\n  }\n}"}], "id": "7a3eee1f-58c2-4a2a-9b4f-45a0d59b7f26", "session_id": "65cb01d2b62f11f0981c0242ac140003"}}`

update 和 create 的传参类似，实例如下

```json
{
    "positionId": 1,
    "positionName": "Managing Director",
    "positionReportsTo": "manager lead",
    "skills": [
        {
            "firstSkill": 1,
            "secondSkill": 4,
            "thirdSkill": "Root Cause Analysis"
        },
        {
            "firstSkill": 1,
            "secondSkill": 4,
            "thirdSkill": "Innovation Management"
        }
    ],
    "id": 53
}
```

