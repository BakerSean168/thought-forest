---
tags:
  - tech/dev
  - type/howto
  - status/growing
description: base_template
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Obsidian MOC]] | [[知识库管理体系 MOC]]

---


# 在项目中嵌入Excalidraw白板 

I'll provide a detailed integration guide for adding Excalidraw whiteboard functionality to your project, including Docker Compose deployment and front-end iframe embedding.

---

# Excalidraw 白板功能集成指南

## 1. 项目简介

Excalidraw 是一个开源、协作式、手绘风格的白板工具，支持自托管和嵌入。你可以通过 Docker Compose 部署 Excalidraw 服务，并在前端页面通过按钮调用、iframe 嵌入实现白板功能。

---

## 2. 后端部署（Docker Compose）

### 2.1 获取 Excalidraw 镜像

Excalidraw 官方仓库包含 Dockerfile，可用于构建自托管服务。你可以直接拉取官方镜像或自行构建：

```bash
# 拉取官方镜像（如有发布）
docker pull excalidraw/excalidraw
```

如需自定义构建，克隆仓库并构建：

```bash
git clone https://github.com/excalidraw/excalidraw.git
cd excalidraw
docker build -t excalidraw/excalidraw .
```

### 2.2 添加到 docker-compose.yaml

在你的 docker-compose.yaml 中添加 Excalidraw 服务：

```yaml
services:
  excalidraw:
    image: excalidraw/excalidraw:latest
    container_name: excalidraw
    ports:
      - "5001:80" # 5001 可自定义，80 为容器默认端口
    restart: unless-stopped
    environment:
      # 可选：配置环境变量
      # - NODE_ENV=production
    # volumes:
    #   - ./excalidraw-data:/app/data # 如需持久化数据
```

然后启动服务：

```powershell
docker-compose up -d excalidraw
```

服务启动后，访问 `http://localhost:5001` 即可打开 Excalidraw 白板。

---

## 3. 前端集成（iframe 嵌入）

### 3.1 按钮触发白板

在你的 Vue 页面中添加一个按钮，点击后弹出白板（iframe）：

```vue
<template>
  <div>
    <button @click="showWhiteboard = true">打开白板</button>
    <div v-if="showWhiteboard" class="whiteboard-modal">
      <iframe
        src="http://localhost:5001"
        width="1000"
        height="700"
        frameborder="0"
        allowfullscreen
      ></iframe>
      <button @click="showWhiteboard = false">关闭</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const showWhiteboard = ref(false)
</script>

<style>
.whiteboard-modal {
  position: fixed;
  top: 10%;
  left: 10%;
  width: 80vw;
  height: 80vh;
  background: #fff;
  z-index: 1000;
  box-shadow: 0 0 20px #0003;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
```

### 3.2 数据交互（可选）

Excalidraw 支持通过 postMessage API 与父页面通信（如导入/导出白板数据）。具体用法见官方文档和 API 说明。

---

## 4. 维护与扩展

- 可将 Excalidraw 服务部署到内网服务器或云主机，前端 iframe 地址相应调整。
- 支持多用户协作、数据导出、定制化开发。
- 推荐定期更新镜像，关注官方安全公告。

---

## 5. 参考链接

- [Excalidraw GitHub](https://github.com/excalidraw/excalidraw)
- [Excalidraw 文档](https://docs.excalidraw.com/)
- [Docker 官方文档](https://docs.docker.com/)

---

如需进一步实现数据交互、权限控制或自定义功能，可参考 Excalidraw 的 API 文档或 npm 包集成方式。需要详细代码或遇到具体问题可随时补充说明。

## 实战

### 总体流程

按钮点击后跳转到对应的域名 xx/excalidraw，然后向 服务器发起请求，nginx 收到后代理到 服务器的某一个端口（运行着 excalidraw 的docker 镜像）

### 环境搭建

在 windows 环境下拉代码出现了 切换到 集成版分支 就会在 changes 里出现修改文件的错误。

改为通过 wsl，在linux 环境下修改代码

[[WSL#使用 vscode]]

最终改为 公司服务器上直接修改
### git workflow 分支创建

切换到 集成版 分支，再使用 `git checkout -b feature/while-board`,创建功能分支


### 镜像构建

[[构建Excalidraw的容器]]
### info

修改文件大致位置：
smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/imports/ui/components/audio/audio-graphql/audio-captions/live

服务器中包所在位置：
/usr/shared/bigbluebutton/html5-client 包放到该文件夹内

服务器的nginx配置是在 
/etc/nginx/sites-available/bigbluebutton
### 打包+部署

```bash
cd /home/llh-program/smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5 && ./deploy.sh

sudo rm -rf /usr/share/bigbluebutton/html5-client/*

sudo mv /home/llh-program/smart-caption/bbb-3.1.0-beta.1/bigbluebutton-html5/dist/* /usr/share/bigbluebutton/html5-client/ 

sudo mv dist/* /usr/share/bigbluebutton/html5-client/ 

sudo systemctl restart nginx
```

nginx 中配置：
```
location /excalidraw/ {
    proxy_pass http://10.248.17.22:3000/;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;

    # 重写页面里的绝对路径，确保资源请求带上 /excalidraw 前缀(否则访问不了资源)
    proxy_set_header Accept-Encoding "";
    sub_filter_once off;
    sub_filter 'src="/'  'src="/excalidraw/';
    sub_filter 'href="/' 'href="/excalidraw/';
    sub_filter 'url("/'  'url("/excalidraw/';
    sub_filter 'fetch("/' 'fetch("/excalidraw/';

  }
```

docker build calidraw

docker build -t dockerfile .  -t 后面是指定打包好镜像的名称
docker-compose.yml
