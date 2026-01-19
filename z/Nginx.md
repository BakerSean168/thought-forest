---
tags:
  - tech/ops/server
  - type/howto
  - status/growing
description: Nginx Web 服务器配置与使用
created: 2025-09-29T18:17:00
updated: 2025-12-07T17:00:00
---

# Nginx

**上级索引**：[[Nginx MOC]]

## 安装配置

### 通过 apt 安装

```bash
sudo apt update

sudo apt install nginx

sudo systemctl start nginx 
sudo systemctl enable nginx

sudo systemctl status nginx
```

### 配置结构

Nginx 的核心配置文件是 `/etc/nginx/nginx.conf`，但为了方便管理，我们通常在以下两个目录下进行配置：
- **`/etc/nginx/sites-available/`：** 存放所有可用的站点配置文件。
- **`/etc/nginx/sites-enabled/`：** 存放实际生效的配置文件，这些文件是通过软链接（Symbolic Link）链接到 `sites-available` 目录下的文件的。

sudo rm /etc/nginx/sites-enabled/nezha.conf
sudo ln -s /etc/nginx/sites-available/nezha.conf /etc/nginx/sites-enabled/

`nginx -V` 查看 Nginx 安装目录、编译参数、配置文件位置(--conf-path)...  

`/etc/nginx` 配置文件位置  
`/usr/share/nginx` 静态文件位置  

nginx -t 检测文件配置是否有问题
nginx -s reload 重新加载

### Web服务器

nginx.conf

```
events{}

http{
    
        include /etc/nginx/mine.types;
        include /etc/nginx/conf.d/*.conf;
}
```

/etc/nginx/conf.d/default.conf

```
server {
    listen 80;
    server_name localhost;

    location /app {
        root /var/www/localhost;
    }
}
```

#### location

**路径匹配**

存在：
localhost/app/index.html
localhost/apple/index.html
1. 
location /app {
	root /var/www/localhost;
} //匹配路径包含 /app 的地址

URL localhost/app //在app当前所在的文件夹下寻找， 不能找到
URL localhost/app/ //在app文件夹中寻找， 能找到
URL localhost/app/index.html //能访问
URL localhost/apple/index.html //能访问
//更灵活但容易暴露其他文件
2. 
location = /app/index.html {
	root /var/www/localhost;
} //访问的路径要与实际文件完全一致
//不太灵活
3. 
location ~ /app/index[2-4].html {
	root /var/www/localhost;
} //使用正则表达式

*匹配优先级*：
1. = 精确
2. ^~ 优先前缀
3. ~和~* 正则
4. 空格 普通前缀

**重定向**

1. return
location / {
	return 307 /app/index.html
}

2. rewrite
rewrite /temp /app/index.html

3. try_files
location / {
	try_files $url $url/ =404;
} //先匹配第一个，失败后第二个，最后404；可通过 error_page 404 route 自定义404页面

#### 反向代理

```
server {
    listen 80;
    server_name localhost;

    root /var/www/localhost;
    index index.html;
    error_page 404 /404.html;

    location app1 {
        proxy_pass http://localhost:3000;
    }

    location app2 {
        proxy_pass http://localhost:3001;
    }
}
```

#### 负载均衡

```
upstream backend-servers {
    server localhost:3000 weight=2;
    server localhost:3001 weight=6;
}

server {
    listen 80;
    server_name localhost;

    root /var/www/localhost;
    index index.html;
    error_page 404 /404.html;

    location / {
        proxy_pass http://backend-servers;
    }

}
```
