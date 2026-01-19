---
tags:
  - tech/dev/system
  - type/concept
  - status/seed
description: Windows
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Resource]] | [[生活 MOC]]

---


# Windows

## 系统镜像

Windows 操作系统主要区分为 Ghost 系统和 MSDN 系统。

- Ghost 系统  
	Ghost 系统一般由个人或团队修改官方原版 Windows 操作系统而成，因为 Ghost 系统安装时间短，安装后可能默认已经激活，所以深受装机商们的喜爱。但 Ghost 系统可能会造成系统不稳定，网上各种 Ghost 系统可能存在后门、自带各种第三方软件、篡改浏览器主页等行为。Ghost 系统有利也有弊，Ghost 系统详细介绍请前往 https://baike.baidu.com/item/ghost系统/8452726 进行了解。  
- MSDN 系统 
	MSDN 的全称为 Microsoft Developer Network，意为《微软开发者网络》，是微软一个期刊产品，专门介绍各种编程技巧。  
	真正意义上的 MSDN 指的是 MSDN Subscription（MSDN 订阅）。这是一种付费订阅服务，可以将微软几乎所有可开发软件以开发为目的使用，包括各种操作系统和应用程序。  
	MSDN 系统为官方原版，由 MSDN 订阅者从微软 MSDN 订阅中心下载，因为 Windows 操作系统属于商业软件，所以安装后一般默认为未激活，需要使用有效的正版密钥激活。  

## Windows 权限管理

### 用户账户

#### 介绍

Windows的用户帐户是对计算机用户身份的识别，且本地用户帐户和密码信息是存储在本地计算机上的（即由：安全账户管理器【Security Accounts Manager】负责SAM（Security Accounts Manager）数据库的控制和维护。  
SAM数据库则是位于注册表的【计算机\HKEY_LOCAL_MACHINE\SAM\SAM】下，受到ACL保护），SAM文件在【C:\Windows\System32\config】路径下。SAM对应的进程：lsass.exe （Local Security Authority Process）。  
通过本地用户和组，可以为用户和组分配权利和权限，从而限制用户和组执行某些操作的能力。 不同的用户身份拥有不同的权限，每个用户包含一个名称和一个密码，用户帐户拥有唯一的安全标识符(Security Identifier，SID)。

#### 默认用户账户信息

| 默认用户账户 | 含义 | 说明 |
| --- | --- | --- |
| Administrator | 管理员用户 | 管理计算机(域)的内置帐户（默认禁用） |
| DefaultAccount | 默认账户 | 系统管理的用户帐户（默认禁用） |
| defaultuser0 | 默认用户 | （默认禁用） |
| Guest | 来宾用户 | 提供给访客人员使用（默认禁用） |
| WDAGUtilityAccount | Windows Defender用户 | 系统为 Windows Defender 应用程序防护方案管理和使用的用户帐户（默认禁用） |

#### 常用命令

| 序号 | 命令 | 命令说明 |
| --- | --- | --- |
| 1 | `net user` | 查看系统账户 |
| 2 | `net user Cm0001 123456 /add` | 创建用户：Cm0001，密码：123456 |
| 3 | `net user Cm0001 987654` | 修改用户 Cm0001 的密码为 987654 |
| 4 | `net user Cm0001` | 查看用户 Cm0001 的属性 |
| 5 | `net localgroup administrators Cm0001 /add` | 提升用户 Cm0001 的权限为管理员 |
| 6 | `net user Cm0001 /del` | 删除用户 Cm0001 |

### 组账户

#### 介绍

组账户是一些用户的集合；且组内的用户自动拥有组所设置的权限。
- 组账户是用户账户的集合，用于组织用户账户
- 为一个组授予权限后，则该组内的所有成员用户自动获得改组的权限
- 一个用户账户可以隶属于多个组，而这个用户的权限就是所有组的权限的合并集

#### 内置组账户

| 组账户名称 | 含义 | 说明 |
| --- | --- | --- |
| Administrators | 管理员组 | 默认情况下，Administrators 组中的用户对计算机/域有不受限制的完全访问权。功能包括：<br>① 管理文件：系统磁盘中的文件只能由 Administrators 组的账户进行更改。<br>② 更改系统安全设置：安装新功能、更改计算机网络设置、对服务器选项进行设置等操作需要 Administrators 组中的用户进行操作。<br>默认情况下，Administrator 账户处于禁用状态。当启用时，具有对计算机的完全控制权限，并根据需要向用户分配权力和访问控制权限。建议设置强密码，不可从管理员组删除，但可以重命名或禁用。 |
| Guests | 来宾用户组 | 提供给没有用户帐户但需要访问本地计算机资源的用户使用。该组的成员无法永久改变其桌面的工作环境，最常见的默认成员为 Guest。<br>① 通常没有修改系统设置和安装程序的权限。<br>② 没有创建或修改任何文档的权限，只能读取系统信息和文件。<br>③ 默认情况下禁用 Guest 用户，且此账户默认为 Guests 组成员，其他权限需由管理员组中的用户成员赋予。 |
| Power Users | 功能用户组 | 组内用户具备比 Users 组更多的权利，但比 Administrators 组少。例如，可以创建、删除、更改本地用户帐户；管理本地计算机内的共享文件夹与共享打印机；自定义系统设置等。<br>① 不可以更改 Administrators。<br>② 无法夺取文件的所有权。<br>③ 无法备份与还原文件。<br>④ 无法安装或删除设备驱动程序。<br>⑤ 无法管理安全与审核日志。 |
| Users | 标准用户组 | 权限低于 Administrators 组，但高于 Guests 组。<br>① 可以进入“网络和共享中心”并查看网络连接状态，但无法修改连接属性。<br>② 无法关闭防火墙。<br>③ 无法安装软件。<br>④ 无法对该用户文件夹以外的 C 盘进行修改。 |
| Remote Desktop Users | 远程桌面用户组 | 组内成员拥有远程桌面登录的权限；默认 Administrators 组内的成员都拥有远程桌面的权限。<br>Remote Desktop Users 组的作用是保障远程桌面服务的安全运行。将需要连接远程桌面的用户分到 Remote Desktop Users 组中是一种更安全的方式。 |

#### 常用命令

| 序号 | 组命令 | 说明 |
| --- | --- | --- |
| 1 | `net localgroup` | 查看系统的组 |
| 2 | `net localgroup testgroup /add` | 新建一个 testgroup 的组 |
| 3 | `net localgroup testgroup Cm0001 /add` | 将用户 Cm0001 加入 testgroup 组中 |
| 4 | `net localgroup testgroup` | 查看 testgroup 组内的成员 |
| 5 | `net localgroup testgroup Cm0001 /del` | 将用户 Cm0001 从 testgroup 组中移除 |
| 6 | `net localgroup testgroup /del` | 删除 testgroup 组 |
| 7 | `net localgroup "remote desktop users" hack /add` | 将用户 hack 加入 remote desktop users 组中 |
| 8 | `net localgroup "remote desktop users"` | 查看 remote desktop users 组内的成员 |
| 9 | `net localgroup "remote desktop users" hack /del` | 将用户 hack 从 remote desktop users 组中移除 |

### 访问控制

当一个用户试图访问一个文件或者文件夹的时候，NTFS 文件系统会检查用户使用的帐户或者账户所属的组是否在此文件或文件夹的访问控制列表（ACL）中。如果存在，则进一步检查访问控制项 ACE，然后根据控制项中的权限来判断用户最终的权限  

![alt text](Windows/image.png)

| 访问控制名称 | 含义 | 说明 |
| --- | --- | --- |
| Access Control List | 访问控制列表(ACL) | 访问权限决定着某个用户可以访问的文件和目录。对于每一个文件和文件夹，由安全描述符(SD)规定了安全数据。安全描述符决定安全设置是否对当前目录有效，或者它可以被传递给其他文件和目录。 |
| Access Control environment | 访问控制环境（ACE） | 访问控制项ACE，是指访问控制实体，用于指定特定用户/组的访问权限，是权限控制的最小单位。<br>① 完全控制：对文件或者文件夹可执行所有操作；<br>② 修改：可以修改、删除文件或文件夹；<br>③ 读取和执行：可以读取内容，并且可以执行应用程序；<br>④ 列出文件夹目录：可以列出文件夹内容，此权限只针对文件夹存在，文件无此权限；<br>⑤ 读取：可以读取文件或文件夹的内容；<br>⑥ 写入：可以创建文件或文件夹；<br>⑦ 特别的权限：其他不常用的权限，比如删除权限的权限； |
| New Technology File System | 文件系统（NTFS） | 文件夹的NTFS权限 (文件夹内的文件或文件夹会默认继承上一级目录的权限)。<br>① 完全控制：对文件或者文件夹可执行所有操作；<br>② 修改：可以修改、删除文件或文件夹；<br>③ 读取和执行：可以读取内容，并且可以执行应用程序；<br>④ 读取：可以读取文件的内容；<br>⑤ 写入：可以修改文件的内容；<br>⑥ 特殊权限：其他不常用的权限，比如删除权限的权限； |
| Security Identifiers | 安全标识符（SID） | SID的说明：<br>安全标识符是标识用户、组和计算机帐户的唯一的号码。在第一次创建该帐户时，将给网络上的每一个帐户发布一个唯一的 SID。Windows 2000 中的内部进程将引用帐户的 SID 而不是帐户的用户或组名。如果创建帐户，再删除帐户，然后使用相同的用户名创建另一个帐户，则新帐户将不具有授权给前一个帐户的权力或权限，原因是该帐户具有不同的 SID 号。安全标识符也被称为安全 ID 或 SID。<br>SID的作用：<br>用户通过验证后，登陆进程会给用户一个访问令牌，该令牌相当于用户访问系统资源的票证，当用户试图访问系统资源时，将访问令牌提供给 Windows NT，然后 Windows NT 检查用户试图访问对象上的访问控制列表。如果用户被允许访问该对象，Windows NT将会分配给用户适当的访问权限。访问令牌是用户在通过验证的时候有登陆进程所提供的，所以改变用户的权限需要注销后重新登陆，重新获取访问令牌。 |

## shift + del 强制删除文件
