---
tags:
  - type/resource
  - status/growing
  - tech/dev
description: Win11 电脑使用 WSL2 记录
created: 2025-10-22T20:48:42
updated: 2025-12-07T17:00:00
---

# WSL 

**上级索引**：[[Resource]] | [[Linux MOC]]

## 基础概念

## 使用指南

### 下载安装 wsl 服务

Windows 下 `win` 键打开搜索栏，搜索 wsl，点击打开就能自动下载

下载完双击只是启动 wsl 服务

### 下载 wsl 镜像

#### wsl 命令行

```
使用“wsl.exe --list --online' ”列出可用的分发
和 “wsl.exe --install <Distro>” 进行安装。
```

#### 微软官网（好像Microsoft Store 也行）[旧版 WSL 的手动安装步骤 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#downloading-distributions)

#### 使用国内镜像源下载（推荐）

[清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/)

当时下载的文件直达：
[ubuntu-24.04.3-wsl-amd64.wsl](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/24.04.3/ubuntu-24.04.3-wsl-amd64.wsl "ubuntu-24.04.3-wsl-amd64.wsl")

下载完后双击打开

```powershell
正在安装: C:\wsl\ubuntu-24.04.3-wsl-amd64.wsl
已成功安装分发。可以通过 “wsl.exe -d Ubuntu-24.04” 启动它==]
正在启动 Ubuntu-24.04...
wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。
Provisioning the new WSL instance Ubuntu-24.04
This might take a while...
Create a default Unix user account: alexander
New password:
Retype new password:
passwd: password updated successfully
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

alexander@LAPTOP-RS6OFKUJ:/mnt/c/wsl$
```

*速度快的一*

## 实战经验

[[wsl中主机代理导致的网络问题]]

### wsl --install 没反应,一段时间后报错

按任意键修复即可

```
wsl: WSL 安装似乎已损坏 (错误代码： Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG)。
按任意键修复 WSL，或 CTRL-C 取消。
此提示将在 60 秒后超时。
没有注册类
错误代码: Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG
```

### 使用 vscode

在 `~/work-program/` 中 clone 项目，然后进入项目，输入 `code .`，会自动下载 vscode-server，通过 remote-develop 在 windows 下的 vscode 中编写代码。

### 配置代理

```
# wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。
```

初始进入看到上面的提示，没在意，后面使用 copilot 时，发现问题：
通过 wsl 来编写项目时，copilot 是也装在 wsl 上，然后 copilot 直接断开连接了

#### 解决方案

[[[解决“wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理”_wsl: 检测到 localhost 代理配置,但未镜像到 wsl。nat 模式下的 wsl 不支持-CSDN博客](https://blog.csdn.net/m0_62815143/article/details/141285660)

[wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。和 ConnectionRefusedError: [Errno 11]_人工智能_远处一座山-GitCode 开源社区](https://gitcode.csdn.net/65e83c781a836825ed78af39.html?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-6-135048661-blog-141285660.235%5Ev43%5Epc_blog_bottom_relevance_base9&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Eactivity-6-135048661-blog-141285660.235%5Ev43%5Epc_blog_bottom_relevance_base9&utm_relevant_index=7)
打开电脑的控制面板，找到网络和Internet ，Internet 选项，连接，局域网设置，取消代理模式，选择自动检测。

当时先试用了第一个添加 `.wslconfig` 的配置文件的方法，没有生效。然后使用了第二个，成功了（个锤子）。（此时好像要给 clash 下载服务模式，启动 TUN MODE，否则还是没有代理）

**最终方法**(成功）：
在 windows 搜索菜单中 使用 wsl setting，把 网络 中的 网络模式 改为 Mirrored。（好像也是生成 `.wslconfig`配置文件，我看了一下，是开头的标识不同 exprimental vs wsl2 ）

[win11快速解决“wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理” - 知乎](https://zhuanlan.zhihu.com/p/15762609815)

### 打开闪退

只装了 wsl 服务，没装分发版镜像。。。。

## 经验总结

### 命令速查


| 命令                       | 说明                     |
| ------------------------ | ---------------------- |
| wsl --shutdown           | 关闭镜像                   |
| wsl --list --verbose     | 列出所有分发版                |
| wsl --terminate <分发版名称>  | 停止分发版                  |
| wsl --unregister <分发版名称> | 删除分发版                  |
| wsl --terminate <分发版名称>  |                        |
| wsl --list --online      | 可用分发版，还是去镜像网站下载吧，官方太慢了 |


## 信息参考

[设置 WSL 开发环境 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/setup/environment)
