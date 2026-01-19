---
tags: [type/howto, status/evergreen]
description: 如何使用 scoop 安装并配置 go 环境
created: 2025-12-24T19:15:43
updated: 2025-12-24T19:23:57
---

# 使用 scoop 安装并配置 go 环境 

## 安装

unigetui 里面搜索 go 可能不方便，太短了，可以直接命令行安装

```powershell
# 安装 Go 语言环境 
scoop install go
```

## 配置代理

```powershell
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```

## 测试

```powershell
go version

go env
```