---
tags:
  - tech/ops/proxy
  - type/concept
  - status/seed
description: sub-store订阅管理工具
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---


# sub-store 

## 基础概念

## 使用指南


### 配置 nginx 反代

1. 配置 域名的 DNS
2. 生成证书
3. 创建 nginx 配置文件


- [[Certbot]]
- [[Nginx#配置结构]]

```
server {
    listen 80;
    server_name sub.bakersean.cloudns.ch; # 替换为您的 Sub-store 域名

    # 强制将所有 HTTP 请求重定向到 HTTPS
    return 301 https://$host$request_uri;
}

server {
    # 监听 443 端口，启用 SSL/TLS 和 HTTP/2
    listen 443 ssl http2;
    server_name sub.bakersean.cloudns.ch; # 替换为您的 Sub-store 域名

    # Certbot 自动生成的证书路径 (通常是这个路径，请根据实际情况核对)
    ssl_certificate /etc/letsencrypt/live/sub.bakersean.cloudns.ch/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/sub.bakersean.cloudns.ch/privkey.pem;

    # Certbot 推荐的 SSL 参数 (保障安全)
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # 确保 Certbot 运行过一次后会生成此文件

    # 反向代理设置
    location / {
        # 转发到 Sub-store 所在的端
        proxy_pass http://127.0.0.1:3001;

        # 确保后端服务能获取正确的客户端 IP 和域名信息
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # 增加超时设置，防止连接被过早切断
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

## 经验总结

## 信息参考

[GetSomeCats/Substore的云同步操作步骤.md at Surge · getsomecat/GetSomeCats](https://github.com/getsomecat/GetSomeCats/blob/Surge/Substore%E7%9A%84%E4%BA%91%E5%90%8C%E6%AD%A5%E6%93%8D%E4%BD%9C%E6%AD%A5%E9%AA%A4.md)
