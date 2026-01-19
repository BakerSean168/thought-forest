---
tags:
  - tech/ops/ssl
  - type/concept
  - status/seed
description: Certbot免费SSL证书配置工具
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS 资源与综合应用 MOC]] | [[DevOps MOC]]

---


# Certbot 

## 基础概念

快速免费地为您的域名配置 Let's Encrypt 证书

获取 SSL 证书（强烈推荐）

1. **安装 Certbot：**

	```
    sudo apt install certbot python3-certbot-nginx
    ```

2. **获取证书并自动配置 Nginx：** Certbot 会自动修改您在第一部分创建的 Nginx 配置文件，添加 `ssl_certificate` 等参数。

	```
    # 为 Sub-store 获取证书
    sudo certbot --nginx -d sub.yourdomain.com
    # 为 Nezha 面板获取证书
    sudo certbot --nginx -d status.yourdomain.com
    ```

	在运行过程中，Certbot 会询问您一些问题（如是否强制 HTTPS 重定向），按照提示选择即可。

## 使用指南

## 实战经验

## 经验总结

## 信息参考
