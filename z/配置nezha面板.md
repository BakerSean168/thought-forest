---
tags:
  - tech/ops/vps
  - type/howto
  - status/growing
description: 配置nezha面板
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---


# 配置nezha面板 

## 基础概念

## 使用指南

### agent 服务管理

`/opt/nezha/agent/config.yml` -agent 配置文件（用于打开 debug）

| 命令                                          | 说明          |
| ------------------------------------------- | ----------- |
| `systemctl status nezha-agent.service`      | 查看agent服务状态 |
| `sudo journalctl -u nezha-agent.service -f` | 查看服务日志      |


## 实战经验

### 安装

```bash
请自行选择您的安装方式：
1. Docker
2. 独立安装
请输入选择 [1-2]：1
哪吒监控管理脚本
--- https://github.com/nezhahq/nezha ---
3.  安装面板端
4.  修改面板配置
5.  重启并更新面板
6.  查看面板日志
7.  卸载管理面板
————————————————-
8.  更新脚本
————————————————-
9.  退出脚本

请输入选择 [0-6]: 1
> 安装
> 修改配置
正在下载 Docker 脚本
请输入站点标题: title
请输入暴露端口: (默认 8008)11688
请指定安装命令中预设的 nezha-agent 连接地址 （例如 example.com:443）www.bakersean.cloudns.ch:1443
是否希望通过 TLS 连接 Agent？（影响安装命令）[y/N]y
请指定后台语言
1. 中文（简体）
2. 中文（台灣）
3. English
请输入选项 [1-3]1
Dashboard 配置 修改成功，请稍等 Dashboard 重启生效
```
### 配置 nginx 反代（失败ing）

1. 配置 域名的 DNS
2. 生成证书
3. 创建 nginx 配置文件

- [[Certbot]]
- [[Nginx#配置结构]]

```
server {
    listen 80;
    server_name nezha.bakersean.cloudns.ch; # 替换为您的 Nezha 域名

    # 强制将所有 HTTP 请求重定向到 HTTPS
    return 301 https://$host$request_uri;
}

server {
    # 监听 443 端口，启用 SSL/TLS 和 HTTP/2
    listen 443 ssl http2;
    server_name nezha.bakersean.cloudns.ch; # 替换为您的 Nezha 域名

    # Certbot 自动生成的证书路径 (请根据实际情况核对)
    ssl_certificate /etc/letsencrypt/live/nezha.bakersean.cloudns.ch/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/nezha.bakersean.cloudns.ch/privkey.pem;

    # Certbot 推荐的 SSL 参数
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    # 反向代理设置
    location / {
        # 转发到 Nezha 面板所在的端口
        proxy_pass http://127.0.0.1:11688;

        # 确保后端服务能获取正确的客户端 IP 和域名信息
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # **重要：WebSocket 支持** (Nezha 面板需要此设置才能实时更新数据)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
~
```

```
# ----------------------------------------------------
# Upstream 定义 (指向宿主机或容器的网络)
# ----------------------------------------------------
upstream dashboard {
    # 由于您将容器内部 8008 映射到宿主机 8008，所以这里写 127.0.0.1:8008
    server 127.0.0.1:8008; 
    keepalive 512;
}

# ----------------------------------------------------
# 1. HTTP 重定向 (强制 HTTPS)
# ----------------------------------------------------
server {
    listen 80;
    server_name nezha.bakersean.cloudns.ch; 
    return 301 https://$host$request_uri;
}

# ----------------------------------------------------
# 2. HTTPS/gRPC/WebSocket 主配置
# ----------------------------------------------------
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name nezha.bakersean.cloudns.ch; 

    # 替换为您的证书路径（使用 Certbot 或 NPM 路径）
    ssl_certificate      /etc/letsencrypt/live/nezha.bakersean.cloudns.ch/fullchain.pem;
    ssl_certificate_key  /etc/letsencrypt/live/nezha.bakersean.cloudns.ch/privkey.pem;

    ssl_stapling on;
    ssl_session_timeout 1d;
    ssl_protocols TLSv1.2 TLSv1.3;
    
    # 无 CDN，禁用/注释掉 CDN 相关的设置
    # underscores_in_headers on;
    # set_real_ip_from 0.0.0.0/0; 
    # real_ip_header CF-Connecting-IP;

    # ----------------------------------------------------
    # A. gRPC 代理配置 (Agent 通信专用)
    # ----------------------------------------------------
    # V1 版本开始，gRPC 和 Web 访问都通过默认 8008 端口通信
    location ^~ /proto.NezhaService/ {
        grpc_set_header Host $host;
        # 使用 $remote_addr 获取客户端真实 IP
        grpc_set_header nz-realip $remote_addr; 
        
        grpc_read_timeout 600s;
        grpc_send_timeout 600s;
        grpc_socket_keepalive on;
        client_max_body_size 10m;
        grpc_buffer_size 4m;
        
        # 转发到上游 dashboard 端口 (8008)
        grpc_pass grpc://dashboard; 
    }

    # ----------------------------------------------------
    # B. WebSocket 代理配置 (用于面板的实时终端/文件管理)
    # ----------------------------------------------------
    location ~* ^/api/v1/ws/(server|terminal|file)(.*)$ {
        proxy_set_header Host $host;
        # 使用 $remote_addr 获取客户端真实 IP
        proxy_set_header nz-realip $remote_addr; 
        
        proxy_set_header Origin https://$host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
        
        # 转发到上游 dashboard 端口 (8008)
        proxy_pass http://dashboard; 
    }

    # ----------------------------------------------------
    # C. Web 页面访问代理
    # ----------------------------------------------------
    location / {
        proxy_set_header Host $host;
        # 使用 $remote_addr 获取客户端真实 IP
        proxy_set_header nz-realip $remote_addr; 
        
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
        
        # 启用此行避免无法正确读取访问的协议
        proxy_set_header X-Forwarded-Proto $scheme; 
        
        # 转发到上游 dashboard 端口 (8008)
        proxy_pass http://dashboard; 
    }
}
```

### 不修改+关闭tls

成功连接 agent

### 网速监听

添加服务


| 名称  | Google    | Cloudflare |
| --- | --------- | ---------- |
| 目标  | 8.8.8.8   | 1.1.1.1    |
| 类型  | ICMP Ping | ICMP Ping  |
|     |           |            |
|     |           |            |
|     |           |            |

## 经验总结

## 信息参考

[哪吒监控 - 服务器监控与运维工具 | 使用文档](https://nezha.wiki/)
