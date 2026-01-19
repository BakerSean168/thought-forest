---
tags:
  - type/howto
  - status/evergreen
description: 如何xxx
---


# 美化终端WindowsTerminal 

## 基础配置

Windows Terminal 中有： 
- 主题设置
- 背景设置
- 等

## 字体颜色（schemas） 设置

https://windowsterminalthemes.dev/

## 字体设置

https://github.com/ryanoasis/nerd-fonts/

## oh-my-posh 设置

使用 winget 安装

```powershell
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\jblab_2021.omp.json" | Invoke-Expression
```

## 参考

https://www.youtube.com/watch?v=-G6GbXGo4wo