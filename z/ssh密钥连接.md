---
tags:
  - tech/security/security
  - type/concept
  - status/seed
description: ssh密钥连接
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[安全 MOC]] | [[学科知识 MOC]]

---


# ssh密钥连接

1. 下载 vscode 的 Remote Development 插件
2. 获取虚拟机的 ip 地址，并在本机主机看能不能 ping 通
3. 在本地主机生成 ssh 密钥对，并将公钥传到虚拟主机的 ~/.ssh 文件中
4. 配置本地主机的 ssh 配置文件

## 基础概念

[[ssh]]

## 实战经验

### 生成 ssh 密钥

生成 ssh 密钥需要 ssh 相关服务，一般在下载 git 后会自带 openssh。

ssh-keygen

用于为“ssh”生成、管理和转换认证密钥，它支持RSA和DSA两种认证密钥。  

选项：  

```
-b：指定密钥长度；
-e：读取openssh的私钥或者公钥文件；
-C：添加注释；
-f：指定用来保存密钥的文件名；
-i：读取未加密的ssh-v2兼容的私钥/公钥文件，然后在标准输出设备上显示openssh兼容的私钥/公钥；
-l：显示公钥文件的指纹数据；
-N：提供一个新密语；
-P：提供（旧）密语；
-q：静默模式；
-t：指定要创建的密钥类型。
```

eg：  
`ssh-keygen -t rsa -b 4096 -f ubu2004` 

windows 下生成在用户目录下，建议剪贴到 .ssh 中统一保存  
linux 生成在用户目录的 .ssh 文件中  

### 配置目标（虚拟机）

1. 确保本地和目标都有 ssh 服务

```bash
# 查看本机的 ssh 服务状态
# On the remote Linux machine
sudo systemctl status ssh
# or
sudo systemctl status sshd
```

如果没有服务，则安装：  

```bash
sudo apt update
sudo apt install openssh-server
```

### 配置本地主机的 ssh 配置文件

vscode 提供了图形化操作：  
点击 ssh 行的右侧 ⚙️ 选项，选择当前用户下的 .ssh\config 文件，进行配置  
配置项如下：  

```
Host Ubuntu20.04
    HostName 192.168.97.125
    User zhangsan
    port 22
    IdentityFile "C:\Users\zhangsan\.ssh\ubu2004"
```

此时左侧 ssh 下出现了配置的主机项，可以直接连接

私钥放在本地用户的.ssh目录下（本机）
公钥必须放在用户的 `~/.ssh/authorized_keys` 文件中（服务器）

### 连接 github 仓库注意事项

测试与 Github ssh 连接的命令：

```bash
ssh -T git@github.com
```

- 默认使用本地默认目录下的默认文件（id_rsa）作为连接密钥，其他文件需要手动指定。
- 权限要设置成 `600` `chmod 600 id_rsa`

### ssh 指定认证文件

#### 方式 1：临时指定密钥（单次生效）

推送代码或连接 SSH 时，用 `-i` 参数指定密钥路径，比如：

bash

```bash
# 测试 SSH 连接（指定 ajie 密钥）
ssh -T -i ~/.ssh/ajie git@github.com

# 推送代码（指定 ajie 密钥）
GIT_SSH_COMMAND="ssh -i ~/.ssh/ajie" git push -u origin main
```

#### 方式 2：配置 config 文件（永久生效，推荐）

在 `.ssh` 目录下创建 / 编辑 `config` 文件，直接绑定 “GitHub 主机” 和 “你的 ajie 密钥”，后续无需每次指定：

1. 编辑配置文件：
	
	bash

```bash
vim ~/.ssh/config
```

2. 添加以下内容（复制粘贴，注意路径正确）：

```bash
# 配置 GitHub 专用规则
Host github.com
  HostName github.com       # 远程主机地址（固定）
  User git                  # 连接用户名（GitHub 固定用 git）
  IdentityFile ~/.ssh/ajie  # 你的非默认密钥路径
  IdentitiesOnly yes        # 强制使用指定密钥，不找默认密钥（可选，更安全）
 ```

3. 保存退出后，直接用正常命令即可：

```bash
ssh -T git@github.com  # 会自动用 ajie 密钥
git push -u origin main # 推送也会自动用 ajie 密钥
```

## 经验总结

windows 生成地私钥默认是没有后缀地，但是好像要添加后缀兼容性更高？（不该pem后缀，指定了私钥文件仍然要输入密码，添加后缀就好了）

10 主机 8989端口

# 高级

端口映射
