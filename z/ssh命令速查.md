---
tags:
  - tech/ops/linux
  - type/howto
  - status/growing
description: ssh命令速查
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Linux MOC]] | [[后端开发 MOC]]

---



# SSH 常用命令速查表

以下表格按 **使用场景分类**，整理了 SSH 核心命令（含密钥认证相关操作），包含命令格式、参数说明及实战示例，便于日常查阅和使用。

|   |   |   |   |
|---|---|---|---|
|场景分类|命令格式|参数 / 选项说明|实战示例|
|**一、基础远程登录**||||
|1. 密码认证登录|ssh [选项] 用户名@服务器IP/域名 -p 端口号|- -p 端口号：指定 SSH 服务端口（默认 22，可选）- 无选项：默认密码验证|ssh ubuntu@192.168.1.100（默认 22 端口）ssh root@server.com -p 2222（指定 2222 端口）|
|2. 密钥认证登录|ssh [选项] 用户名@服务器IP/域名 -p 端口号 -i 私钥路径|- -i 私钥路径：指定本地私钥文件（若私钥非默认路径 ~/.ssh/id_rsa，必须添加）- 免密：需提前将公钥上传至服务器|ssh ubuntu@192.168.1.100 -i ~/.ssh/my_ssh_key（用自定义私钥登录）ssh centos@10.0.0.5（私钥在默认路径，直接免密登录）|
|3. 远程执行单命令|ssh 用户名@服务器IP "命令内容"|双引号包裹远程命令，支持管道、变量（需注意转义特殊字符）|ssh ubuntu@192.168.1.100 "df -h"（查看远程磁盘占用）`ssh root@server.com "cat /etc/os-release|
|**二、密钥认证配置**||||
|1. 生成密钥对|ssh-keygen [选项] -f 密钥文件路径|- -f 密钥文件路径：指定密钥保存路径（默认 ~/.ssh/id_rsa）- -t rsa：指定算法（默认 RSA，可选 ECDSA 等）- -C "备注"：添加密钥备注（如邮箱）|ssh-keygen -t rsa -f ~/.ssh/my_key -C "my-server-key"（生成名为 my_key 的 RSA 密钥对）|
|2. 上传公钥到服务器|ssh-copy-id [选项] 用户名@服务器IP -p 端口号 -i 公钥路径|- -i 公钥路径：指定本地公钥文件（默认 ~/.ssh/id_rsa.pub）- 自动将公钥追加到服务器 ~/.ssh/authorized_keys|ssh-copy-id ubuntu@192.168.1.100（默认公钥上传）ssh-copy-id -i ~/.ssh/my_key.pub root@server.com -p 2222（指定公钥和端口）|
|3. 验证密钥权限|chmod [权限] 私钥文件/公钥文件|- 私钥权限必须为 600（仅所有者可读可写，防止泄露）- 公钥权限可设为 644（所有者可读，其他可读取）|chmod 600 ~/.ssh/my_key（修复私钥权限）chmod 644 ~/.ssh/my_key.pub（修复公钥权限）|
|**三、文件传输（基于 SSH）**||||
|1. scp 上传文件|scp -P 端口号 -i 私钥路径 本地文件路径 用户名@服务器IP:服务器目标路径|- -P 端口号：大写 P（区别于 ssh 的小写 p），指定端口- -r：递归传输目录（可选）- -i 私钥路径：密钥认证（可选，密码认证省略）|scp -P 2222 -i ~/.ssh/my_key ~/local.txt ubuntu@192.168.1.100:/home/ubuntu/（上传文件到服务器）scp -r ~/local_dir root@server.com:/tmp/（递归上传目录，默认端口）|
|2. scp 下载文件|scp -P 端口号 -i 私钥路径 用户名@服务器IP:服务器文件路径 本地目标路径|参数同上传，仅交换 “本地路径” 和 “服务器路径”|scp -P 2222 ubuntu@192.168.1.100:/home/ubuntu/remote.txt ~/downloads/（下载文件到本地）scp -r -i ~/.ssh/my_key root@server.com:/var/log/ ~/log_backup/（下载日志目录）|
|3. sftp 连接|sftp -P 端口号 -i 私钥路径 用户名@服务器IP|交互式文件传输，支持 ls/cd/put/get 等命令- -P 端口号：指定端口，-i 私钥路径：密钥认证|sftp -P 2222 ubuntu@192.168.1.100（密码认证连接 sftp）sftp -i ~/.ssh/my_key root@server.com（密钥认证连接 sftp）|
|**四、端口转发（隧道）**||||
|1. 本地端口转发|ssh -L 本地端口:目标服务IP:目标服务端口 用户名@服务器IP -p SSH端口 -N|- -L 本地端口:目标IP:目标端口：本地端口映射到远程目标服务- -N：仅建立隧道，不登录（可选，后台运行用）- 用于访问远程内网服务（如数据库）|ssh -L 3306:10.0.0.2:3306 ubuntu@192.168.1.100 -N（本地 3306 映射到内网 MySQL 服务）ssh -L 8080:172.17.0.3:80 root@server.com -p 2222 -N（本地 8080 映射到远程内网 Web 服务）|
|2. 远程端口转发|ssh -R 远程服务器端口:本地服务IP:本地服务端口 用户名@服务器IP -p SSH端口 -N|- -R 远程端口:本地IP:本地端口：远程服务器端口映射到本地服务- 用于外部通过服务器访问本地服务（如本地开发环境）|ssh -R 8080:localhost:8080 ubuntu@192.168.1.100 -N（服务器 8080 映射到本地 8080 Web 服务）ssh -R 5432:127.0.0.1:5432 root@server.com -p 2222 -N（服务器 5432 映射到本地 PostgreSQL 服务）|
|**五、其他常用操作**||||
|1. 查看 SSH 版本|ssh -V|大写 V，查看客户端版本（服务端版本需登录后执行 sshd -V）|ssh -V（输出示例：OpenSSH_8.2p1 Ubuntu-4ubuntu0.9）|
|2. 清理 known_hosts|ssh-keygen -R 服务器IP/域名|删除客户端缓存的服务器旧公钥（解决 “Host key verification failed” 错误）|ssh-keygen -R 192.168.1.100（删除 IP 对应的旧公钥）ssh-keygen -R server.com（删除域名对应的旧公钥）|
|3. 后台保持连接|ssh -o ServerAliveInterval=60 用户名@服务器IP|-o ServerAliveInterval=60：每 60 秒发送一次心跳包，防止连接超时断开|ssh -o ServerAliveInterval=60 ubuntu@192.168.1.100（长时间操作时保持连接）|

### 表格使用说明

1. **参数优先级**：命令中 选项（如 -i、-P）需紧跟 ssh/scp 命令，用户名@服务器IP 放在最后（端口号 -p 除外，需紧跟服务器地址）。

2. **密钥认证注意事项**：

- 私钥文件必须保存在本地，且权限严格设为 600（否则 SSH 会拒绝使用，提示 “Permissions too open”）。

- 若服务器修改了 SSH 端口，所有命令（含 ssh-copy-id、scp）都需添加 -p 端口号（scp 用 -P 端口号，大写 P）。

3. **后台运行隧道**：端口转发命令添加 -fN 选项可后台运行（-f 后台，-N 不登录），示例：ssh -fN -L 3306:[10.0.0.2:3306](http://10.0.0.2:3306) ubuntu@[192.168.1.100](http://192.168.1.100)，关闭时需用 ps aux | grep ssh 找到进程并 kill。
