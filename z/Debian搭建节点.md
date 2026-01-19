---
tags:
  - tech/ops/vps
  - type/howto
  - status/growing
description: Debian系统上搭建代理节点教程
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS 资源与综合应用 MOC]] | [[代理节点]]

---


# Debian搭建节点 

## 1. 基本安全设置(可选)

1. 该 root 密码
2. 改 ssh 默认端口（安装 UFW 防火墙并启用）

## 2. 启用 BBR

在Debian 启用可以显著提高网络的速度和可靠性。BBR可以降低数据包丢失，增加吞吐量，优化网络性能。

Debian检查是否开启命令：

```
sysctl net.ipv4.tcp_congestion_control net.core.default_qdisc
```

一键开启命令：
```
echo -e "\nnet.core.default_qdisc=fq\nnet.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf && sysctl -p
```

输入以下，则已经开启

```
net.ipv4.tcp_congestion_control = bbr
net.core.default_qdisc = fq
```

## 3. 域名解析

> [!NOTE] Title
> 关闭小云朵

## 4. 用一键证书脚本申请好证书

要在云服务器配置中打开 HTTP、HTTPS、SSH 的端口

```bash
# 一键申请脚本
bash <(curl -sL https://raw.githubusercontent.com/qichiyuhub/auto-ssl-cert/refs/heads/main/setup.sh)

# 结果
证书文件: /etc/ssl/yu.ykszckj.com/yu.ykszckj.com.crt
私钥文件: /etc/ssl/yu.ykszckj.com/yu.ykszckj.com.key
```

## 5. 安装singbox

```bash
bash <(curl -sL "https://raw.githubusercontent.com/qichiyuhub/autoshell/refs/heads/main/install_singbox.sh")
```

## 6. 编辑配置文件

**编辑完上传到 /etc/sing-box 目录覆盖原始配置**

## 7. 启动sing-box


| 命令                                            | 说明        |
| --------------------------------------------- | --------- |
| `sudo systemctl enable sing-box`              | 启用服务，开机自启 |
| `sudo systemctl start sing-box`               | 运行        |
| `sudo systemctl restart sing-box`             |           |
| `sudo journalctl -u sing-box --output cat -f` | 看日志       |
## 8. 客户端订阅生成

使用substore转为多种客户端订阅

## 伪装域名选择

## 参考资料

https://www.qichiyu.com/750.html
https://lot.pm/enable-bbr-on-debian.html
