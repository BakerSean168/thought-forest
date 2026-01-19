---
tags:
  - tech/ops/git
  - type/howto
  - status/evergreen
description: 应用代码修改到另一个版本（git）
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# 应用代码修改到另一个版本 

## 问题详情

生成环境的 bbb 项目使用的是 3.0 版本的

新功能（一个小的直播模块）却是在 3.1 版本 的 bbb 上修改的

现在想要直接把 直播模块 移植到 生产环境却报错了，需要降级，把 修改直接应用到 3.0 版本的源码上。
## 解决方案

利用 git 的 diff 功能

查看更改
```bash
# 直接指定仓库根目录（bbb-3.1.0-beta.1），过滤其下的 bigbluebutton-html5 目录
git -C /home/llh-program/smart-caption/bbb-3.1.0-beta.1 \
  diff --name-status bb6f6e8..HEAD -- bigbluebutton-html5
```

保存更改到补丁
```bash
# 生成从 bb6f6e8 到 HEAD 之间，bigbluebutton-html5 目录的所有变更补丁
git -C /home/llh-program/smart-caption/bbb-3.1.0-beta.1 \
  diff bb6f6e8..HEAD -- bigbluebutton-html5 > ~/bbb-html5-changes.patch
```


## 实战经验
## 经验总结、

白板所有相关文件

白板按钮
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/imports/ui/components/excalidraw/button/component.jsx // 直接新增 x

功能按钮
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/imports/ui/components/actions-bar/component.jsx // 直接全部替换 x

meetingClientSettings
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/imports/ui/Types/meetingClientSettings.ts //直接全部替换

en.json
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/public/locales/en.json // 直接全部替换 x

字幕相关
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/imports/ui/components/audio/audio-graphql/audio-captions/live/component.tsx // 直接全部替换

docker-bbb3.0-dev/repos/bigbluebutton/bigbluebutton-html5/imports/ui/components/actions-bar/component.jsx （上面处理了，功能按钮处）

下面的好像没有关系
nginx
settings
~/work_program/smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/private/config/settings.yml
## 信息参考
