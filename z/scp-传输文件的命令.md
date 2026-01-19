---
tags:
  - tech/ops/linux
  - type/howto
  - status/evergreen
description: SCP安全文件传输命令速查
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Linux MOC]] | [[后端开发 MOC]]

---


# SCP-传输文件的命令

SCP（Secure Copy Protocol）是基于SSH协议的安全文件传输工具，核心优势是在传输过程中对数据进行加密，避免明文传输带来的安全风险，常用于Linux、macOS等类Unix系统，Windows系统可通过PowerShell或第三方工具（如PuTTY的pscp）使用。

## 一、基础概念

1. **核心原理**：依赖SSH（Secure Shell）协议建立安全连接，默认使用22号端口，所有文件数据和指令在传输前都会经过加密处理。
2. **关键特性**：
   - 安全性：通过SSH的加密机制，防止数据被监听或篡改。
   - 便捷性：直接在命令行操作，无需额外搭建复杂的服务（如FTP）。
   - 跨平台：支持不同操作系统间的文件传输，如Linux→Windows、macOS→Linux。
3. **常见术语**：
   - 本地主机：执行SCP命令的计算机，文件的“源”或“目标”之一。
   - 远程主机：需要与本地主机交换文件的计算机，需知道其IP地址和SSH登录账号。
   - 绝对路径：文件在系统中的完整位置（如`/home/user/docs/file.txt`），SCP命令中推荐使用，避免路径混淆。

## 二、使用指南

SCP的命令格式核心为“指定源文件/目录”和“指定目标位置”，主要分为4类常用场景，需注意：执行命令前需确保远程主机已开启SSH服务，且本地主机有权限登录远程主机。

### 1. 本地文件传输到远程主机

**命令格式**：`scp [本地文件绝对路径] [远程用户]@[远程主机IP]:[远程目标路径]`
- 示例：将本地`/home/abc/test.txt`传到远程主机`192.168.1.100`的`/root/docs`目录下，远程用户为`root`

  ```bash
  scp /home/abc/test.txt root@192.168.1.100:/root/docs
  ```

- 说明：执行后会提示输入远程用户的SSH密码，密码正确则开始传输，传输完成会显示进度和耗时。

### 2. 远程文件下载到本地主机

**命令格式**：`scp [远程用户]@[远程主机IP]:[远程文件绝对路径] [本地目标路径]`
- 示例：将远程`192.168.1.100`的`/root/docs/report.pdf`下载到本地`/home/abc/downloads`目录

  ```bash
  scp root@192.168.1.100:/root/docs/report.pdf /home/abc/downloads
  ```

### 3. 传输目录（含子文件/子目录）

需添加`-r`参数（recursive，递归传输），否则会提示“不是常规文件”报错。
**命令格式**：`scp -r [本地目录路径] [远程用户]@[远程主机IP]:[远程目标路径]`（本地→远程）；反向则为远程→本地。
- 示例：将本地`/home/abc/project`目录传到远程`192.168.1.100`的`/root/data`下

  ```bash
  scp -r /home/abc/project root@192.168.1.100:/root/data
  ```

### 4. 指定SSH端口（非默认22端口）

若远程主机SSH端口不是默认的22（如改为2222），需用`-P`参数（大写P，区分小写p的“保留文件权限”参数）指定端口。
- 示例：通过2222端口下载远程文件

  ```bash
  scp -P 2222 root@192.168.1.100:/root/docs/note.txt /home/abc
  ```

### 5. 使用密钥

```powershell

scp -i ~/.ssh/code.pem -r root@47.108.204.117:/output1 D:/

scp -i ~/.ssh/XiangGangVPS2v1g.pem -r ./dist root@8.218.51.159:/

```
"C:\Users\Alexander\.ssh\hhc_key.pem"
解释：

- -i ~/.ssh/code.pem：指定私钥文件路径。
- -r：递归复制整个目录（可以传输文件夹）。
- root@47.108.204.117:/output1：云服务器上的目录路径。
- D:/：Windows 本地目录路径。

## 三、实战经验

1. **传输大文件时显示进度**：默认SCP传输大文件（如超过1GB）时无进度条，可添加`-v`参数（verbose，详细模式），实时查看传输速度和进度。
   - 示例：`scp -v /home/abc/large.zip root@192.168.1.100:/root/storage`
2. **保留文件权限和属性**：添加`-p`参数（preserve），传输后保持文件的修改时间、访问时间和权限（如读写权限、所有者）不变，适合传输配置文件或可执行文件。
   - 示例：`scp -p /home/abc/config.conf root@192.168.1.100:/etc`
3. **避免重复传输**：SCP不支持“增量传输”（仅传输修改过的文件），若需频繁同步目录，可先通过`diff`或`md5sum`命令对比本地与远程文件，只传输差异文件；或改用`rsync`工具（兼容SCP，支持增量同步）。
4. **处理传输中断**：若传输中途断开（如网络波动），SCP不会自动续传，需重新执行命令从头传输。可改用`scp -C`（启用压缩）减少传输数据量，降低中断概率；或使用`screen`/`tmux`工具在后台执行传输命令，避免终端关闭导致中断。

## 四、经验总结

1. **优先用绝对路径**：避免使用相对路径（如`./file.txt`），尤其在远程主机路径中，绝对路径能确保文件准确传输到目标位置，减少“找不到路径”的报错。
2. **区分`-P`和`-p`参数**：`-P`（大写）用于指定SSH端口，`-p`（小写）用于保留文件属性，两者不可混淆，否则会导致命令失效。
3. **大文件/目录的替代方案**：SCP适合小文件（<1GB）或单文件传输，若传输超大文件（>10GB）或复杂目录，建议用`rsync`（支持增量、续传）或`SFTP`（交互式传输，适合多文件选择），效率更高。
4. **安全注意事项**：
   - 避免用root用户直接传输文件，建议用普通用户登录，必要时通过`sudo`获取权限，降低安全风险。
   - 若频繁传输，可配置SSH密钥登录（免密码），替代每次输入密码，同时禁用SSH密码登录，提升主机安全性（需在远程主机`/etc/ssh/sshd_config`中配置`PasswordAuthentication no`）。

## 五、信息参考

1. **官方文档**：Linux系统可通过`man scp`命令查看本地SCP手册，包含完整参数说明和示例；macOS手册同Linux，可在终端执行`man scp`。
2. **SSH服务配置**：远程主机需开启SSH服务，CentOS/RHEL系统可通过`systemctl start sshd`启动，Ubuntu/Debian系统通过`systemctl start ssh`启动；防火墙需开放SSH端口（默认22），如`firewall-cmd --add-port=22/tcp --permanent`（CentOS）或`ufw allow 22`（Ubuntu）。
3. **Windows使用工具**：
   - PowerShell：Windows 10及以上自带OpenSSH客户端，可直接在PowerShell中执行`scp`命令，语法与Linux一致。
   - 第三方工具：PuTTY的`pscp.exe`（命令行）、WinSCP（图形化界面，支持拖放传输，兼容SCP/SSH），适合不熟悉命令行的用户。
4. **常见错误排查**：
   - “Permission denied”：远程目标路径无写入权限，需修改远程目录权限（如`chmod 755 /root/docs`）或更换有权限的用户。
   - “Connection refused”：远程主机未开启SSH服务，或端口被防火墙拦截，需检查SSH服务状态和端口开放情况。
   - “No such file or directory”：本地文件路径错误或远程路径不存在，需确认路径拼写正确，远程路径不存在时可先通过`ssh`登录远程主机创建目录（如`ssh root@192.168.1.100 "mkdir -p /root/docs"`）。

