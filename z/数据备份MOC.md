---
tags: [tech/ops, type/moc, status/growing]
description: ops
created: 2025-12-14T20:44:47
updated: 2025-12-14T20:45:48
---

# 数据备份MOC

## overview

### 可选备份工具

github、cloud storage、移动硬盘

### 需要备份数据

主力电脑：
- unigetUI中的软件信息  -自带备份，通过 github gist
- etc 中的配置文件 -github
- document 文件夹 -OneDrive
- 各种项目代码 -github
- obsidian笔记 -GitHub

服务器：
- 系统镜像 -移动硬盘
- 各个系统的配置 -移动硬盘+云盘
- 服务的数据 -移动硬盘+云盘




## 系统备份

## 软件备份

怎么做到所有的常用工具都用 winget scoop 等命令行下载工具来快速获取。即使用新电脑，也能快速恢复原有生态

## 备份策略

系统只备份基础镜像 或者加上少数软甲；
然后利用 软件 json 列表（winget 获取软件，scoop 用于开发环境）来快速重新安装

| 工具 | 推荐用途 | 优势 |
| :--- | :--- | :--- |
| **`winget`** | **通用软件 (GUI Apps)** | 官方支持、安装过程与传统安装无异、适合主流大型应用（如 Office、浏览器、流媒体软件）。 |
| **`scoop`** | **开发环境和命令行工具 (CLI)** | **无需管理员权限**、**安装路径隔离**、特别适合 Java、Node.js、Python、Git 等经常需要**多版本切换**或**环境变量配置**的开发工具。 |

浏览器：
- [[Google Chrome]]
- edge windows应该会自带

视频：
- [[mpv]]

图书：
- [[Calibre2]]
- [[SumatraPDF]]

图片(查看、处理）：
- [[IrfanView]]
- [[image-matting]]

社交媒体：
- [[Telegram]]
- [[QQ]]

压缩软件：
- 7zip

画图（流程图）：
- draw.io

笔记：
- Obsidian

离线OCR:
- [[Umi-OCR]]

文件传输：
- [[LocalSend]]

游戏：
- [[Steam]]
- [[Epic Games]]
- wegame
- [[oopz]]
- [[perfecworld]]
- [[5EClient]]

dev：
- [[VSCode MOC]]
- [[APIFox]]
- [[Git]]
- [[Wispr Flow]]
- nodejs环境（nvm）
- go环境
- java环境
- python环境

ops：
- [[WindTerm]]

proxy：
- [[sparkle]]