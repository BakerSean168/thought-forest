---
tags:
  - tech/ops/network
  - type/howto
  - status/growing
description: Hiddify-Next自建订阅
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[计算机网络 MOC]] | [[前端基础 MOC]]

---



# 自建订阅（Sub）系统指南

## 概述

自建订阅系统可以将多个节点自动生成：

- Clash.Meta YAML
- sing-box JSON
- Hiddify-Next 专用配置
- Base64 订阅链接

适合：机场、自建节点多的用户、想统一管理配置的人。

## 架构思路

典型 Sub 系统结构：

- **核心**：后端生成器（Node.js / Python / Go）
- **数据来源**：配置文件、数据库、线上节点列表
- **输出格式**：Clash YAML / sing-box JSON / Base64 / Hiddify 配置
- **前端**（可选）：用于展示二维码 + 链接

## 推荐技术栈

### ✅ Node.js（最常用）

- 快速处理字符串、模板
- 可用 JS 模板生成 Clash / sing-box
- 可搭配 Express 暴露 API

### ✅ Python

- 模板灵活
- YAML/JSON 处理方便

### ✅ Go（高性能）

- 更适合大规模机场
- 大部分现有订阅系统都使用 Go

---

## 基本实现步骤

### ✅ 1. 节点数据结构

准备一个 nodes.json：

```json
[
  {
    "name": "HK-01",
    "type": "vless",
    "server": "1.2.3.4",
    "port": 443,
    "uuid": "xxxx-xxxx",
    "tls": true,
    "flow": "xtls-rprx-vision"
  }
]
```

### ✅ 2. 模板文件（Clash YAML 示例）

```yaml
proxies:
{{#each nodes}}
  - name: "{{this.name}}"
    type: {{this.type}}
    server: {{this.server}}
    port: {{this.port}}
    uuid: {{this.uuid}}
    tls: {{this.tls}}
{{/each}}
```

（注意：Hiddify-Next 支持 Clash.Meta）

### ✅ 3. 使用 JS 生成配置

示例（Node.js）：

```js
const fs = require("fs");
const Handlebars = require("handlebars");

const nodes = require("./nodes.json");
const tpl = fs.readFileSync("template.yaml", "utf8");
const compile = Handlebars.compile(tpl);

const output = compile({ nodes });
fs.writeFileSync("sub.yaml", output);
```

### ✅ 4. 生成订阅链接

你可以把内容 Base64 编码：

```bash
base64 -w 0 sub.yaml > sub.txt
```

然后放在 HTTP 服务器上：

```
https://yourdomain.com/sub.txt
```

Hiddify-Next 将自动识别。

---

## sing-box JSON 订阅输出示例

若你要输出 sing-box 格式：

```json
{
  "outbounds": [
    {
      "type": "vless",
      "tag": "HK-01",
      "server": "1.2.3.4",
      "server_port": 443,
      "uuid": "xxxx"
    }
  ]
}
```

生成方式与 YAML 类似。

---

## 部署 API（给机场 / 多设备更新）

Node.js 最常见：

```js
app.get("/sub", (req, res) => {
  const yaml = generateYAML();
  const encoded = Buffer.from(yaml).toString("base64");
  res.send(encoded);
});
```

Hiddify-Next 直接使用：

```
https://yourdomain.com/sub
```

---

## 使用 Hiddify 官方 sub 系统（更简单）

如果你不想从零开始搭建，可以使用官方工具：

✅ [https://github.com/hiddify/hiddify-config](https://github.com/hiddify/hiddify-config)

特点：

- 自动生成 sing-box + Clash 同步订阅
- 支持节点批量导入
- 自带模板
- 支持 Hysteria2 / Reality
- 支持自动分流规则

---

## 进阶功能（可按需扩展）

- 节点负载均衡
- 自动测速（写入 meta 信息）
- 自动禁用失效节点
- 节点限速 / 标签系统
- 不同用户不同订阅（JWT / Token）
- 流媒体优选路线

如你需要，我可以继续补充完整 Node.js 版、Python 版或 Go 版的“完整可运行订阅系统”。

---



