---
tags:
  - tech/ops/linux
  - type/howto
  - status/growing
description: SSH全面使用文档
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Linux MOC]] | [[后端开发 MOC]]

---


# SSH 全面使用文档

## 一、基础概念

### 1. 定义

SSH（Secure Shell，安全外壳协议）是一种**加密的网络传输协议**，主要用于在不安全的网络环境中，为客户端与服务器之间建立安全的远程连接，替代传统的明文传输协议（如 Telnet、FTP），防止数据在传输过程中被窃听、篡改或伪造。

### 2. 核心作用

- **远程登录**：通过 SSH 客户端连接远程服务器，获得命令行交互界面，执行操作（如服务器管理、文件修改）。
- **安全文件传输**：借助 SSH 衍生工具（如 scp、sftp）实现加密的文件上传 / 下载。
- **端口转发**：将本地端口与远程服务器端口绑定，实现 “跳板访问”（如访问内网服务）或加密本地应用流量。

### 3. 工作原理（加密流程）

SSH 采用 “非对称加密 + 对称加密” 结合的方式，确保连接安全，核心流程如下：

1. **握手阶段**：客户端向服务器发起连接请求，服务器返回自身的公钥（用于身份验证）和支持的加密算法列表。
2. **身份验证**：客户端验证服务器公钥（首次连接需手动确认，后续通过本地缓存的 “known_hosts” 文件自动验证），防止 “中间人攻击”。
3. **会话密钥协商**：客户端与服务器通过 “Diffie-Hellman 密钥交换算法” 生成一个**临时的对称会话密钥**（仅本次连接有效）。
4. **数据传输**：后续所有交互数据（命令、输出、文件内容）均使用 “会话密钥” 进行对称加密，保证传输效率与安全性。

### 4. 核心组件

- **SSH 客户端**：安装在本地设备（如电脑、手机）的工具，用于发起连接请求，常见工具包括：
- 命令行工具：Linux/macOS 自带的 ssh 命令、Windows 10+ 自带的 OpenSSH 客户端。
- 图形化工具：PuTTY、Xshell、FinalShell、Termius。
- **SSH 服务端**：运行在远程服务器上的服务（如 OpenSSH Server），监听指定端口（默认 22 端口），接收客户端连接请求，常见实现为 openssh-server（主流 Linux 发行版默认支持）。

## 二、使用指南

### 1. 环境准备

#### （1）服务端配置（Linux 服务器为例）

- **安装 OpenSSH 服务端**：
- Debian/Ubuntu 系统：sudo apt update && sudo apt install openssh-server -y
- CentOS/RHEL 系统：sudo yum install openssh-server -y
- **启动并设置开机自启**：

```
# 启动服务sudo systemctl start sshd  # 部分系统服务名为 ssh（如 Ubuntu）# 设置开机自启sudo systemctl enable sshd# 验证服务状态（查看是否监听 22 端口）sudo systemctl status sshd
```

- **基础防火墙配置**（若开启防火墙，需放行 22 端口）：
- 防火墙（ufw，Ubuntu 常用）：sudo ufw allow 22/tcp && sudo ufw reload
- 防火墙（firewalld，CentOS 常用）：sudo firewall-cmd --add-port=22/tcp --permanent && sudo firewall-cmd --reload

#### （2）客户端准备

- Linux/macOS：无需额外安装，直接打开 “终端”，使用内置 ssh 命令。
- Windows：
- 方式 1：Windows 10+ 开启 “OpenSSH 客户端”（设置 → 应用 → 可选功能 → 添加 “OpenSSH 客户端”），通过 “命令提示符” 或 “PowerShell” 使用 ssh 命令。
- 方式 2：安装图形化工具（如 PuTTY），双击打开后输入服务器 IP 和端口即可连接。

### 2. 核心使用场景

#### （1）基础远程登录（密码验证）

最常用的场景，通过服务器用户名和密码建立连接，命令格式：

```
ssh [用户名]@[服务器IP/域名] -p [端口号]
```

- 参数说明：
- [用户名]：远程服务器的合法用户（如 root、ubuntu、centos）。
- [服务器IP/域名]：服务器的公网 / 内网 IP（如 [192.168.1.100](http://192.168.1.100)）或域名（如 [server.example.com](https://server.example.com)）。
- -p [端口号]：可选参数，默认端口为 22，若服务端修改了端口（如 2222），需指定 -p 2222。
- 示例：

```
# 连接 IP 为 192.168.1.100、用户为 ubuntu 的服务器（默认 22 端口）ssh ubuntu@192.168.1.100# 连接端口为 2222 的服务器ssh root@server.example.com -p 2222
```

- 连接流程：输入命令后，首次连接会提示 “确认服务器公钥”，输入 yes 后，再输入对应用户的密码，验证通过即可进入远程命令行界面。

#### （2）密钥登录（免密码，更安全）

密码登录存在 “密码被暴力破解” 的风险，**密钥登录**通过 “公钥 - 私钥对” 验证身份，无需输入密码，且安全性更高，步骤如下：

1. **本地生成密钥对**：

在客户端终端执行 ssh-keygen 命令，按提示操作（默认一路回车即可，也可设置密钥密码增强安全）：

```
ssh-keygen# 执行后会在 ~/.ssh/ 目录下生成两个文件：# - id_rsa：私钥（本地保存，绝对不能泄露给他人）# - id_rsa.pub：公钥（需上传到远程服务器）
```

2. **将公钥上传到服务器**：

使用 ssh-copy-id 工具自动上传公钥（简化操作），命令格式：

```
ssh-copy-id [用户名]@[服务器IP] -p [端口号]
```

示例：ssh-copy-id ubuntu@[192.168.1.100](http://192.168.1.100)，输入服务器用户密码后，公钥会自动添加到服务器的 ~/.ssh/authorized_keys 文件中（该文件存储允许登录的客户端公钥）。

3. **验证密钥登录**：

直接执行远程登录命令，无需输入密码即可连接：ssh ubuntu@[192.168.1.100](http://192.168.1.100)。

#### （3）远程执行单个命令

无需登录服务器，直接在客户端执行远程命令，格式：

```
ssh [用户名]@[服务器IP] "[命令]"
```

示例：

- 查看远程服务器的系统版本：ssh ubuntu@[192.168.1.100](http://192.168.1.100) "cat /etc/os-release"
- 查看远程服务器的磁盘占用：ssh root@[192.168.1.100](http://192.168.1.100) "df -h"

#### （4）文件传输（scp/sftp）

基于 SSH 协议的加密文件传输工具，替代明文的 FTP。

- **scp（简单文件拷贝）**：
- 本地文件上传到服务器：scp -P [端口号] 本地文件路径 用户名@服务器IP:服务器目标路径

示例：scp -P 22 ~/local-file.txt ubuntu@[192.168.1.100](http://192.168.1.100):/home/ubuntu/

- 服务器文件下载到本地：scp -P [端口号] 用户名@服务器IP:服务器文件路径 本地目标路径

示例：scp -P 22 ubuntu@[192.168.1.100](http://192.168.1.100):/home/ubuntu/remote-file.txt ~/

- 拷贝目录（加 -r 参数）：scp -r -P 22 ~/local-dir ubuntu@[192.168.1.100](http://192.168.1.100):/home/ubuntu/
- **sftp（交互式文件传输）**：

适合多文件 / 目录的复杂传输，类似 FTP 的交互界面，命令：

```
# 连接服务器sftp -P [端口号] 用户名@服务器IP# 常用交互命令：put 本地文件  # 上传本地文件到服务器当前目录get 服务器文件  # 下载服务器文件到本地当前目录ls  # 查看服务器当前目录文件cd  # 切换服务器目录lls  # 查看本地当前目录文件lcd  # 切换本地目录exit  # 退出 sftp 会话
```

#### （5）端口转发（隧道）

通过 SSH 建立 “加密隧道”，实现端口映射，常用于访问内网服务或加密本地流量。

- **本地端口转发（访问远程内网服务）**：

将本地端口映射到远程服务器可访问的 “目标服务端口”，格式：

```
ssh -L 本地端口:目标服务IP:目标服务端口 用户名@服务器IP -p SSH端口
```

示例：远程服务器（[192.168.1.100](http://192.168.1.100)）可访问内网数据库（[10.0.0.2:3306](http://10.0.0.2:3306)），本地通过以下命令映射本地 3306 端口，直接连接 [localhost:3306](http://localhost:3306) 即可访问内网数据库：

```
ssh -L 3306:10.0.0.2:3306 ubuntu@192.168.1.100 -p 22
```

- **远程端口转发（让外部访问本地服务）**：

将远程服务器端口映射到本地服务端口，让外部通过服务器端口访问本地服务，格式：

```
ssh -R 远程服务器端口:本地服务IP:本地服务端口 用户名@服务器IP -p SSH端口
```

示例：本地运行 Web 服务（[localhost](https://localhost:8080)[:8080](https://localhost:8080)），通过以下命令映射服务器 8080 端口，外部访问 服务器IP:8080 即可访问本地 Web 服务：

```
ssh -R 8080:localhost:8080 ubuntu@192.168.1.100 -p 22
```

## 三、实战经验

### 1. 安全加固技巧

#### （1）修改默认端口（避免暴力破解）

SSH 默认端口 22 是暴力破解的主要目标，修改为非标准端口（如 2222）：

1. 编辑 SSH 服务配置文件：sudo vim /etc/ssh/sshd_config
2. 找到 `#Port 22`，去掉注释并修改为 Port 2222（端口范围 1-65535，建议选 1024 以上）
3. 重启 SSH 服务：sudo systemctl restart sshd
4. 放行新端口的防火墙规则（参考 “环境准备” 中的防火墙配置）

#### （2）禁用 root 直接登录

root 是服务器最高权限用户，禁用其 SSH 登录可降低风险：

1. 编辑配置文件：sudo vim /etc/ssh/sshd_config
2. 找到 `#PermitRootLogin yes`，修改为 PermitRootLogin no
3. 重启服务：sudo systemctl restart sshd（后续需用普通用户登录，再通过 sudo 提权）

#### （3）限制允许登录的用户

仅允许指定用户通过 SSH 登录，减少攻击面：

1. 编辑配置文件：sudo vim /etc/ssh/sshd_config
2. 添加配置：AllowUsers user1 user2（仅允许 user1、user2 登录，多个用户用空格分隔）
3. 重启服务：sudo systemctl restart sshd

#### （4）定期清理 known_hosts 缓存

客户端首次连接服务器时，会将服务器公钥保存在 ~/.ssh/known_hosts 文件中。若服务器重装系统或更换公钥，再次连接会提示 “主机密钥验证失败”，此时需删除该服务器对应的公钥记录：

- 方式 1：手动编辑文件：vim ~/.ssh/known_hosts，删除包含服务器 IP / 域名的行。
- 方式 2：用 ssh-keygen 命令自动删除：ssh-keygen -R 服务器IP（如 ssh-keygen -R [192.168.1.100](http://192.168.1.100)）

### 2. 常见问题排查

#### （1）连接超时（Connection timed out）

- 排查方向 1：服务器 IP / 端口是否正确（确认服务器公网 / 内网 IP 无误，端口已放行防火墙）。
- 排查方向 2：网络是否可达（本地 ping 服务器IP 测试连通性，若为云服务器，检查安全组是否开放 SSH 端口）。
- 排查方向 3：SSH 服务是否正常运行（服务器端执行 sudo systemctl status sshd 确认服务未停止）。

#### （2）权限被拒绝（Permission denied）

- 情况 1：密码错误（确认用户名和密码正确，注意区分大小写）。
- 情况 2：密钥登录失败（检查客户端私钥权限是否为 600，服务器 ~/.ssh/authorized_keys 权限是否为 600，且文件所有者为对应用户）。
- 情况 3：配置限制（检查 sshd_config 中 AllowUsers 是否包含当前用户，PermitRootLogin 是否为 no 却用 root 登录）。

#### （3）服务器公钥变更（Host key verification failed）

- 原因：服务器公钥已修改（如重装系统、重新生成 SSH 密钥），客户端缓存的旧公钥不匹配。
- 解决：删除客户端 known_hosts 中该服务器的旧记录（参考 “定期清理 known_hosts 缓存”）。

## 四、信息参考

### 1. 核心配置文件

- **服务端配置文件**：/etc/ssh/sshd_config（SSH 服务的核心配置，如端口、登录权限、密钥验证等）。
- **客户端配置文件**：~/.ssh/config（可选，用于保存常用连接的配置，简化命令，示例如下）：

```
# ~/.ssh/config 示例Host my-server  # 自定义连接别名  HostName 192.168.1.100  # 服务器IP/域名  User ubuntu  # 登录用户名  Port 2222  # SSH端口  IdentityFile ~/.ssh/my-key  # 私钥路径（若用自定义密钥）# 后续连接时，直接输入：ssh my-server
```

- **密钥文件目录**：~/.ssh/（客户端和服务器端均有此目录，存储私钥、公钥、known_hosts 等文件）。

### 2. 常用工具与扩展

- **SSH 客户端工具**：
- 命令行：OpenSSH 客户端（Linux/macOS/Windows 自带）。
- 图形化：Xshell（功能强大，支持多标签、密钥管理）、FinalShell（国产工具，集成文件管理、终端，免费版够用）、Termius（跨平台，支持手机端）。
- **SSH 服务端工具**：
- 主流：OpenSSH Server（Linux 系统默认，开源免费，功能全面）。
- 轻量：Dropbear（适合嵌入式设备或资源有限的服务器，体积小、占用内存少）。

### 3. 官方与文档参考

- OpenSSH 官方文档：[https://www.openssh.com/manual.html](https://www.openssh.com/manual.html)（包含协议细节、配置参数详解）。
- Linux 系统手册：在终端执行 man ssh（查看客户端命令手册）、man sshd_config（查看服务端配置手册）。
- 云服务商文档：阿里云、腾讯云、AWS 等均有 SSH 连接服务器的详细指南（含安全组配置、密钥管理）。

