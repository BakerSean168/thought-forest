---
tags:
  - tech/ops/linux
  - type/howto
  - status/evergreen
description: 虚拟机上部署postgresql
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Linux MOC]] | [[后端开发 MOC]]

---


# 🚀 PostgreSQL 服务器搭建指南（VMware 虚拟机方案）


---

## 📋 一、系统选型与资源分配

### **推荐系统：Ubuntu Server 22.04 LTS**

**选择理由**：
- ✅ **长期支持**（2027 年前持续更新）
- ✅ **生态完善**（PostgreSQL 官方支持优先级高）
- ✅ **无图形界面**（资源占用低，适合服务器）
- ✅ **社区活跃**（遇到问题容易找到解决方案）
- ✅ **与生产环境一致**（90% 的 PostgreSQL 生产服务器使用 Linux）

**其他备选**：
- **Rocky Linux 9**（CentOS 替代品，企业级稳定）
- **Debian 12**（更轻量，但 Ubuntu 更新更频繁）

---

### **资源分配方案**

根据你的开发场景（DailyUse 项目），这是一个单用户开发环境，推荐配置如下：

| 组件                   | 最小配置      | 推荐配置       | 说明                                           |
| ---------------------- | ------------- | -------------- | ---------------------------------------------- |
| **CPU**                | 2 核          | **4 核**       | PostgreSQL 并发连接数与 CPU 核心数强相关       |
| **内存**               | 2 GB          | **4 GB**       | PostgreSQL 默认 `shared_buffers` 占用 25%     |
| **硬盘**               | 20 GB (精简)  | **40 GB 动态** | 使用 VMware 动态磁盘（按需增长，节省宿主机空间） |
| **网络**               | NAT 模式      | **桥接模式**   | 桥接模式可直接获取局域网 IP，便于远程访问      |
| **虚拟化特性**         | 启用 VT-x/EPT | 必须启用       | 提升虚拟机性能                                 |

**硬件检查**（你的物理机需要满足）：
- **宿主机 CPU**: 4 核以上（建议 6 核+）
- **宿主机内存**: 16 GB+（虚拟机 4GB + 宿主机 8GB + 其他应用 4GB）
- **宿主机硬盘**: 剩余 50 GB+

---

## 🛠️ 二、详细安装步骤

### **阶段 1: 创建虚拟机**

#### 1.1 下载 Ubuntu Server 22.04 LTS ISO

```bash
# 官方下载地址（约 1.4 GB）
https://releases.ubuntu.com/22.04/ubuntu-22.04.5-live-server-amd64.iso

# 或使用清华镜像（国内速度快）
https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/22.04/ubuntu-22.04.5-live-server-amd64.iso
```

---

#### 1.2 在 VMware 中创建虚拟机

1. **打开 VMware Workstation** → 点击 **"创建新的虚拟机"**

2. **配置向导**：
   - **安装来源**: 选择 `稍后安装操作系统`
   - **客户机操作系统**: `Linux` → `Ubuntu 64 位`
   - **虚拟机名称**: `PostgreSQL-Server-Dev`
   - **位置**: 选择 SSD（性能更好，避免 C 盘）

3. **硬件配置**：
   ```
   处理器:
   - 处理器数量: 1
   - 每个处理器的核心数量: 4
   
   内存:
   - 4096 MB (4 GB)
   
   网络适配器:
   - 类型: 桥接模式（自动）
   - 勾选"复制物理网络连接状态"
   
   硬盘:
   - 类型: SCSI
   - 大小: 40 GB
   - ✅ 勾选"将虚拟磁盘拆分成多个文件"
   - ✅ 勾选"立即分配所有磁盘空间"（可选，性能更好但占用宿主机空间）
   
   CD/DVD (SATA):
   - ✅ 勾选"启动时连接"
   - 使用 ISO 映像文件: 选择下载的 ubuntu-22.04.5-live-server-amd64.iso
   ```

4. **完成后不要启动**，先进行以下优化：
   - 右键虚拟机 → **设置** → **选项** → **高级**
   - 固件类型: `UEFI`（现代系统标准）
   - 禁用旁路通道缓解: `启用` (提升性能)

---

### **阶段 2: 安装 Ubuntu Server**

#### 2.1 启动虚拟机并安装系统

1. **启动虚拟机** → 选择 `Try or Install Ubuntu Server`

2. **安装配置**（关键步骤）：

   **语言选择**:
   ```
   English（推荐，避免中文乱码问题）
   ```

   **网络配置**:
   ```
   ✅ 使用 DHCP 自动获取 IP（默认）
   记住分配的 IP 地址（如 192.168.1.100）
   ```

   **镜像源配置**:
   ```
   Archive mirror: 
   http://mirrors.tuna.tsinghua.edu.cn/ubuntu（国内推荐）
   ```

   **存储配置**:
   ```
   ✅ 使用整个磁盘（Use an entire disk）
   ⚠️ 不要启用 LVM（除非你需要动态扩展）
   ```

   **用户配置**:
   ```
   Your name: dailyuse
   Server name: pg-server
   Username: dailyuse
   Password: 设置强密码（至少 12 位，包含大小写+数字+特殊字符）
   ```

   **SSH 配置**:
   ```
   ✅ Install OpenSSH server（必选，用于远程连接）
   ❌ 不导入 SSH keys（除非你有 GitHub/Launchpad 账户）
   ```

   **Featured Server Snaps**:
   ```
   ❌ 全部不选（稍后手动安装 PostgreSQL）
   ```

3. **等待安装完成**（约 5-10 分钟）
   - 看到 `Reboot Now` 后按回车
   - **重要**: 重启后立即移除 ISO 镜像（右键虚拟机 → 设置 → CD/DVD → 断开连接）

---

### **阶段 3: 系统初始化配置**

#### 3.1 首次登录并更新系统

```bash
# 登录虚拟机（用户名: dailyuse，密码: 你设置的密码）

# 1. 更新软件包列表
sudo apt update

# 2. 升级所有软件包
sudo apt upgrade -y

# 3. 安装必要工具
sudo apt install -y vim curl wget net-tools htop

# 4. 查看虚拟机 IP 地址（记住这个 IP，后面要用）
ip addr show
# 输出示例: inet 192.168.1.100/24
```

---

#### 3.2 配置静态 IP（推荐，避免 IP 变化）

```bash
# 1. 编辑 Netplan 配置
sudo vim /etc/netplan/00-installer-config.yaml

# 2. 修改为以下内容（替换 IP 和网关为你的实际网络）
network:
  ethernets:
    ens33:  # 网卡名称，通过 ip addr show 确认
      dhcp4: no
      addresses:
        - 192.168.1.100/24  # 静态 IP（替换为你的局域网段）
      routes:
        - to: default
          via: 192.168.1.1  # 网关（通常是路由器 IP）
      nameservers:
        addresses:
          - 8.8.8.8  # Google DNS
          - 114.114.114.114  # 备用 DNS
  version: 2

# 3. 应用配置
sudo netplan apply

# 4. 验证 IP（确保是静态 IP）
ip addr show ens33
```

---

### **阶段 4: 安装 PostgreSQL 16**

#### 4.1 添加官方 APT 仓库

```bash
# 1. 安装必要的依赖
sudo apt install -y postgresql-common

# 2. 运行官方安装脚本（自动添加 PostgreSQL 16 仓库）
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh

# 3. 安装 PostgreSQL 16
sudo apt update
sudo apt install -y postgresql-16 postgresql-contrib-16

# 4. 验证安装
sudo systemctl status postgresql
# 输出: active (running) 表示成功
```

---

#### 4.2 PostgreSQL 基础配置

```bash
# 1. 切换到 postgres 用户
sudo -i -u postgres

# 2. 进入 PostgreSQL 命令行
psql

# 3. 设置 postgres 超级用户密码（生产环境必须）
ALTER USER postgres WITH PASSWORD 'your-strong-password-here';

# 4. 创建开发数据库和用户
CREATE DATABASE dailyuse_dev;
CREATE USER dailyuse WITH PASSWORD 'dev-password-2024';
ALTER DATABASE dailyuse_dev OWNER TO dailyuse;

# 5. 授予权限
GRANT ALL PRIVILEGES ON DATABASE dailyuse_dev TO dailyuse;

# 6. 退出 psql
\q

# 7. 退出 postgres 用户
exit
```

---

#### 4.3 配置远程访问（允许宿主机连接）

```bash
# 1. 编辑 PostgreSQL 配置文件
sudo vim /etc/postgresql/16/main/postgresql.conf

# 找到以下行并修改：
listen_addresses = '*'  # 原来是 'localhost'

# 2. 编辑客户端认证配置
sudo vim /etc/postgresql/16/main/pg_hba.conf

# 在文件末尾添加（允许局域网访问）：
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host    all             all             192.168.1.0/24          scram-sha-256

# 如果想允许所有 IP 访问（开发环境可用，生产禁止）：
# host    all             all             0.0.0.0/0               scram-sha-256

# 3. 重启 PostgreSQL
sudo systemctl restart postgresql

# 4. 验证监听端口
sudo netstat -tunlp | grep 5432
# 输出: 0.0.0.0:5432 表示成功监听所有网卡
```

---

### **阶段 5: 防火墙配置（可选但推荐）**

```bash
# 1. 安装 UFW 防火墙
sudo apt install -y ufw

# 2. 允许 SSH（避免被锁在外面）
sudo ufw allow 22/tcp

# 3. 允许 PostgreSQL
sudo ufw allow 5432/tcp

# 4. 启用防火墙
sudo ufw enable

# 5. 查看状态
sudo ufw status
# 输出:
# Status: active
# To                         Action      From
# --                         ------      ----
# 22/tcp                     ALLOW       Anywhere
# 5432/tcp                   ALLOW       Anywhere
```

---

## 🧪 三、连接测试

### **3.1 从宿主机 Windows 连接**

#### 方法 1: 使用 VSCode + PostgreSQL 扩展

1. **安装扩展**: `PostgreSQL` (by Chris Kolkman)

2. **创建连接**:
   ```json
   {
     "host": "192.168.1.100",
     "port": 5432,
     "database": "dailyuse_dev",
     "user": "dailyuse",
     "password": "dev-password-2024"
   }
   ```

#### 方法 2: 使用 DBeaver（推荐，功能最全）

1. **下载 DBeaver**: https://dbeaver.io/download/
2. **新建连接** → PostgreSQL
3. **填写信息**:
   ```
   Host: 192.168.1.100
   Port: 5432
   Database: dailyuse_dev
   Username: dailyuse
   Password: dev-password-2024
   ```
4. **测试连接** → 成功后保存

---

### **3.2 从你的 DailyUse 项目连接**

编辑 .env:

```env
# PostgreSQL 配置
DATABASE_URL="postgresql://dailyuse:dev-password-2024@192.168.1.100:5432/dailyuse_dev?schema=public"
```

运行 Prisma 迁移测试：

```bash
# 在你的 Windows 宿主机项目目录执行
cd apps/api
npx prisma migrate dev --name init
```

---

## 🔧 四、性能优化配置（可选）

编辑 `/etc/postgresql/16/main/postgresql.conf`:

```bash
# 基于 4GB 内存的推荐配置
shared_buffers = 1GB              # 25% 内存
effective_cache_size = 3GB        # 75% 内存
maintenance_work_mem = 256MB      # 内存维护操作
work_mem = 32MB                   # 单个操作内存
max_connections = 100             # 最大连接数

# 性能监控（开发环境推荐）
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
```

重启生效：
```bash
sudo systemctl restart postgresql
```

---

## 📝 五、日常维护命令

```bash
# 查看 PostgreSQL 状态
sudo systemctl status postgresql

# 重启服务
sudo systemctl restart postgresql

# 查看日志
sudo tail -f /var/log/postgresql/postgresql-16-main.log

# 进入 psql（postgres 用户）
sudo -u postgres psql

# 备份数据库
sudo -u postgres pg_dump dailyuse_dev > backup_$(date +%Y%m%d).sql

# 恢复数据库
sudo -u postgres psql dailyuse_dev < backup_20250118.sql
```

---

## ⚠️ 六、常见问题排查

### **问题 1: 宿主机无法连接虚拟机**

```bash
# 1. 检查虚拟机网络模式（必须是桥接模式）
# VMware → 虚拟机设置 → 网络适配器 → 桥接模式

# 2. 检查宿主机能否 ping 通虚拟机
ping 192.168.1.100

# 3. 检查防火墙
sudo ufw status
```

---

### **问题 2: PostgreSQL 拒绝连接**

```bash
# 1. 检查 PostgreSQL 是否监听外部连接
sudo netstat -tunlp | grep 5432
# 应该看到: 0.0.0.0:5432

# 2. 检查 pg_hba.conf 是否允许你的 IP
sudo cat /etc/postgresql/16/main/pg_hba.conf | grep host

# 3. 查看 PostgreSQL 日志
sudo tail -50 /var/log/postgresql/postgresql-16-main.log
```

---

### **问题 3: 密码认证失败**

```bash
# 1. 确认使用 scram-sha-256 认证方式
# 编辑 pg_hba.conf，确保方法是 scram-sha-256 而非 md5

# 2. 重新设置密码
sudo -u postgres psql
ALTER USER dailyuse WITH PASSWORD 'new-password';
\q

# 3. 重启 PostgreSQL
sudo systemctl restart postgresql
```

---

## 🎯 七、下一步建议

1. **学习 PostgreSQL 管理**:
   - 定期备份（使用 `pg_dump` + cron）
   - 监控性能（使用 `pg_stat_statements`）
   - 学习查询优化（`EXPLAIN ANALYZE`）

2. **安全加固**:
   - 更换默认端口 5432（`postgresql.conf` 中修改 `port`）
   - 使用 SSL 连接（配置 `ssl = on`）
   - 定期更新系统（`sudo apt update && sudo apt upgrade`）

3. **快照管理**:
   - 在 VMware 中创建快照（安装完成后立即创建）
   - 重大变更前创建快照（可快速回滚）

---

## 📚 参考资源

- [PostgreSQL 官方文档](https://www.postgresql.org/docs/16/)
- [Ubuntu Server 官方文档](https://ubuntu.com/server/docs)
- [Prisma + PostgreSQL 最佳实践](https://www.prisma.io/docs/guides/database/postgresql)

---

🎉 **完成！** 你现在拥有一个生产级别的 PostgreSQL 开发环境了。如果遇到问题，随时提问！

