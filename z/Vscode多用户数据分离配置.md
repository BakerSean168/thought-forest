---
tags:
  - tech/app/vscode
  - type/howto
  - status/growing
description: VSCode多用户数据分离配置
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VSCode MOC]] | [[Resource]]

---


# Vscode多用户数据分离配置 

## 问题详情

当有多个账号，并且每个账号都有 copilot Pro 时，vscode 没有切换 copilot 登录账号的方法。
此时需要完全退出当前在 VS Code 中的 GitHub 登录，然后使用另一个账号重新登录。

## 解决方案（多用户配置）

### 方法一：复制便携版文件夹（简单直接）

VS Code 便携版的所有配置、插件、缓存等数据都保存在自身文件夹中，因此可以通过复制整个便携版目录，实现多用户空间的完全隔离：
复制便携版目录
找到你的 VS Code 便携版文件夹（例如命名为 VSCode-Portable），将其复制一份，命名为不同的名称（例如 VSCode-Portable-User2）。
此时两个文件夹完全独立，各自的配置、插件、登录账号等互不影响。
分别启动
分别双击两个文件夹中的 Code.exe（Windows）或 Code（macOS/Linux），即可同时运行两个独立的用户空间。

### 方法二：通过命令行指定不同用户目录（无需复制文件夹）

如果不想复制整个便携版文件夹，可以通过命令行参数指定不同的用户数据目录，共享同一个程序文件，仅隔离配置数据：
创建用户目录在任意位置新建两个文件夹，作为不同用户的配置目录（例如 D:\vscode-data-user1 和 D:\vscode-data-user2）。
通过命令行启动打开终端（CMD/PowerShell/ 终端），切换到 VS Code 便携版的 bin 目录（例如 VSCode-Portable\bin），执行以下命令启动：

```bash
PS D:\ide\Microsoft VS Code> .\Code.exe --user-data-dir "D:\CodeSpace\vscode-user\jjc\"

code --user-data-dir "D:\vscode-data-user2"
（如果是 macOS/Linux，命令为 ./code --user-data-dir "/path/to/user1"）
```


## 实战经验

## 经验总结

## 信息参考
