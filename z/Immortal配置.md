---
tags:
  - type/howto
  - status/evergreen
  - tech/ops/network
description: 记录一下无科学条件时的 Immortal 配置，使用 nikki 达到可以科学上网。
created: 2026-01-01T10:22:41
updated: 2026-01-01T13:27:35
---

# Immortal配置 

## 准备

没有科学条件时极有可能只能通过离线安装，所以需要准备好离线安装包（需要本地能连GitHub）
- nikki安装包
另外的数据库可以通过 github 镜像下载

## 网络配置

不知道为什么刚进去连 baidu 都 ping 不通
能 ping 8.8.8.8 应该是 DNS 服务器出了问题

依次输入以下命令，将 DNS 指向你的主路由或公共 DNS：
```bash
uci set network.lan.dns='192.168.0.101 223.5.5.5' 
uci commit network 
/etc/init.d/network restart
```

## 扩容

```ash
opkg update
opkg install bash ca-bundle ca-certificates fdisk resize2fs
```

```
wget -qO- https://raw.githubusercontent.com/sirpdboy/other/master/expand.sh | bash
```

## 镜像写入

```
qm importdisk 103 /var/lib/vz/template/iso/imm local-lvm
```

## 配置 nikki

https://github.com/nikkinikki-org/OpenWrt-nikki

### 数据库安装

数据库也需要科学环境，但可以通过镜像安装

asn
```shell
# 下载 GeoLite2-ASN 数据库
wget -O GeoLite2-ASN.mmdb "https://gh.llkk.cc/https://github.com/xishang0128/geoip/releases/download/latest/GeoLite2-ASN.mmdb"

```

mmdb，重命名为 geoip.metadb
```shell
# 下载 Country-lite 数据库
wget -O geoip.metadb "https://gh.llkk.cc/https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/country-lite.mmdb"

```

geoip
```shell
# 下载 GeoIP 数据库 (Lite版)
wget -O geoip.dat "https://gh.llkk.cc/https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geoip-lite.dat"

```

geosite 重命名文件 GeoSite.dat
```shell
# 下载 GeoSite 数据库
wget -O GeoSite.dat "https://gh.llkk.cc/https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geosite.dat"

```

## 进阶

### 自动更新

通过 定时重启 + Cron 表达式 在指定时间 更新

## 管理面板

通过 混入配置 - 外部配置 - API 监听 可以查看修改访问端口

## 备份与恢复

导出 ImmortalWrt（OpenWrt）的配置主要有两种方式：**通过网页后台（LuCI）和通过命令行（SSH）**。

由于你之前提到是在 PVE 虚拟机里运行，建议优先使用网页版，最直观。

### 方法一：通过网页后台 (最简单、推荐)

这是最通用的方法，导出的文件是一个 `.tar.gz` 压缩包，包含了所有的系统设置。

1. 登录 ImmortalWrt 后台。
2. 进入菜单：**系统 (System) -> 备份/升级 (Backup / Flash Firmware)**。
3. 在“备份/恢复”选项卡中，找到 **“生成备份 (Generate archive)”** 按钮。
4. 点击后，浏览器会自动下载一个名为 `backup-ImmortalWrt-xxxx.tar.gz` 的文件。

**注意：**

- **它备份了什么：** 默认只备份 `/etc/config/` 下的所有设置（网口、Nike 插件、防火墙、DNS 等）以及你手动指定的自定义文件。
- **它不备份什么：** 不备份你安装的**插件本体（二进制文件）**、**Docker 镜像**以及硬盘里的电影/数据。重装系统后，你需要重新安装插件，然后导入这个配置，设置才会生效。

---

### 方法二：通过命令行 SSH (适合备份到电脑或自动化)

如果你习惯用终端，或者网页打不开了，可以用这个方法。

1. 使用 SSH 工具连接到你的路由器。
2. 执行以下命令生成备份文件：

	```
    sysupgrade -b /tmp/backup.tar.gz
    ```

3. 然后你可以使用 SCP 或 WinSCP 工具把 `/tmp/backup.tar.gz` 拷贝到你的电脑上。

---

### 方法三：导出完整的“磁盘镜像” (针对 PVE 玩家)

如果你是想**整体迁移**或者**怕折腾坏了**想留个全尸（包含插件本体、Docker、所有数据），在 PVE 里直接用“快照”或“备份”功能更好：

1. 在 PVE 左侧菜单选择你的虚拟机（102）。
2. 点击 **“备份” (Backup)** -> **“立即备份”**。
3. 这样生成的是一个完整的虚拟机镜像，如果你扩盘重装失败了，可以一键恢复到现在的状态。

---

### 进阶建议：如何备份“Nike”插件或自定义脚本？

有些用户会自己写脚本放在 `/root` 文件夹，或者修改了某些不在默认备份范围内的文件。

- 自定义备份列表：
	在 系统 -> 备份/升级 -> 配置 (Configuration) 选项卡中，你可以看到一个列表。在这里输入你想一起备份的文件路径（例如 /root/myscript.sh），保存。
	之后再点“生成备份”，这些文件就会被打包进去。

### 总结

- 如果你只是想**重装固件后快速恢复上网设置和插件参数**：用方法一（导出的包只有几十 KB）。
- 如果你是想**为了扩盘做准备**：建议先用 PVE 备份整个虚拟机（方法三），然后去重装，万一新系统玩不转，还能秒回旧版本。
