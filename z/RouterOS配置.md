---
tags: [type/howto, status/evergreen, tech/hardware/network]
description: 如何xxx
created: 2025-12-31T19:40:35
updated: 2025-12-31T19:43:33
---

# RouterOS配置 

## 安全

密码
访问方式 ip servuce list

## 网卡基础

### 1. 网口改名

有数据的是 LAN 口，另一个是 WAN 口

### 2. DHCP

DHCP client 默认的给他删除

### 3. 桥接

不同，跳过

## 网络设置

### 1. LAN

LAN 口 的 ip address 设置

### 2. WAN

WAN 口通过 DHCP 或者 拨号获取

### 3. NET 设置

srcnet
action ： masq 开头

网络地址转换规则，这样 WAN 口才能正常使用

### 4. DNS

有默认 运营商的，也可以继续添加
勾选 Allow Remote Requests，可以将 路由 ip 作为 DNS 来源

### 5. DHCP Server

使用向导

### 6. 防火墙

两个入站转发拒绝+两个入站转发允许
端口转发允许 5000 到 443

159.148.147.244

## 备份与恢复

在 winbox 的shell 中执行如下命令,来备份
```shell
# “=” 后面是生成配置文件的名字
/export file=config
```

在 file 中下载保存

里面的备份文件本质是 命令集合（不包含隐私部分，密码），可以直接在命令行中全部执行来恢复，也可以用如下命令

```shell
/import file=config.rsc
```

## 参考

https://www.youtube.com/watch?v=3zvTMxB3MQg

博客 https://www.qichiyu.com/702.html