---
tags:
  - tech/dev/project
  - type/resource
  - status/growing
description: DDCC项目开发文档与Excalidraw白板集成
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[项目实践 MOC]] | [[职业发展 MOC]]

---
# DDCC

一个档案管理项目

- Element-plus
- Vue3

## 业务实现
### 白板功能添加

- 部署 docker 版的 Excalidraw 作为白板功能[[Excalidraw]]
- 使用自带的 pdf 作为白板，添加快捷切换按钮

> [!NOTE] 佛了
> 后面直接换方案，不要 excalidraw 的白板功能了



### 进行bbb版本降级

生成环境是 3.0.8 的 docker 版本，开发环境是 3.1.0 的编译版本。字幕出问题了，得把修改代码重新应用到 3.0.8 中。

#### 改动文件

1. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/actions-bar/component.jsx （修改，会议布局主页面，注释掉excalidraw，导入快捷切换白板按钮）
2. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/actions-bar/toggle-presentation-button/component.jsx （新增，切换白板与附件的按钮）
3. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/audio/audio-graphql/audio-captions/live/component.tsx （修改）
4. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/excalidraw/button/component.tsx （excalidraw 按钮）
5. /home/llh-program/docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/public/locales/en.json （国际化）


===

1. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/actions-bar/component.jsx （修改，会议布局主页面，注释掉excalidraw，导入快捷切换白板按钮）
2. docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/actions-bar/toggle-presentation-button/component.jsx （新增，切换白板与附件的按钮）
3. /home/llh-program/docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/public/locales/en.json （修改，国际化）

域名地址：


### 字幕样式优化

[[Docker项目修改（ddcc优化字幕）]]