---
tags:
  - type/howto
  - status/evergreen
description: 如何xxx
---


# git子模块移除 


## powershell

```powershell
# 先要删除根目录下的 gitmodule 文件

# 删除每个子模块的 .git 目录
PS D:\workProgram\docker-bbb\docker> Get-ChildItem -Path . -Recurse -Directory -Filter ".git" | Remove-Item -Recurse -Force

# 预览每个子模块的 .git 文件
PS D:\workProgram\docker-bbb\docker> Get-ChildItem -Directory | Get-ChildItem -Filter ".git" -Recurse -Force
# 删除每个子模块的 .git 文件
PS D:\workProgram\docker-bbb\docker> Get-ChildItem -Directory | Get-ChildItem -Filter ".git" -Recurse -Force | Remove-Item -Force -Recurse

# 清除缓存
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-etherpad-plugin
rm 'repos/bbb-etherpad-plugin'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-etherpad-skin
rm 'repos/bbb-etherpad-skin'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-pads
rm 'repos/bbb-pads'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-playback
rm 'repos/bbb-playback'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-webhooks
rm 'repos/bbb-webhooks'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-webrtc-recorder
rm 'repos/bbb-webrtc-recorder'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bbb-webrtc-sfu
rm 'repos/bbb-webrtc-sfu'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/bigbluebutton
rm 'repos/bigbluebutton'
PS D:\workProgram\docker-bbb\docker> git rm --cached repos/freeswitch
```

## bash

```powershell
# 先要删除根目录下的 gitmodule 文件
rm .gitmodules

# 删除每个子模块的 .git 目录
find . -mindepth 2 -name ".git" -exec rm -rf {} +

# 预览每个子模块的 .git 文件
PS D:\workProgram\docker-bbb\docker> Get-ChildItem -Directory | Get-ChildItem -Filter ".git" -Recurse -Force
# 删除每个子模块的 .git 文件
PS D:\workProgram\docker-bbb\docker> Get-ChildItem -Directory | Get-ChildItem -Filter ".git" -Recurse -Force | Remove-Item -Force -Recurse

# 清除缓存
git rm --cached repos/*
```