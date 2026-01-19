---
tags:
  - tech/ops
  - type/debug
  - status/seed
description: git克隆报错
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
tool: git
---

> [!info] **上级索引**
> [[Git MOC]] | [[DevOps MOC]]

---


# git克隆报错 

## 代理配置

```bash
# 配置代理
git config --global http.proxy http://127.0.0.1:7890 
git config --global https.proxy http://127.0.0.1:7890

# 查看配置
git config --global --get http.proxy 
git config --global --get https.proxy

# 取消代理
git config --global --unset http.proxy 
git config --global --unset https.proxy
```

## 问题一

```bash
root@live-bbb:/home/llh-program# git clone https://github.com/excalidraw/excalidraw.git
Cloning into 'excalidraw'...
error: RPC failed; curl 16 Error in the HTTP2 framing layer
fatal: expected flush after ref listing
```

通常与网络连接或 HTTP 协议兼容性有关，具体原因和解决方法如下：

### **可能的原因**

1. **网络连接不稳定**：HTTP2 协议对网络稳定性要求较高，若网络波动、延迟高或带宽不足，可能导致数据传输中断，触发帧层错误（`HTTP2 framing layer`）。
2. **HTTP2 协议兼容性问题**：部分环境（如老版本 curl、代理服务器、防火墙）可能对 HTTP2 支持不佳，导致握手或数据传输失败。
3. **仓库体积较大**：Excalidraw 作为开源项目，包含历史提交记录、依赖等，若克隆时传输的数据量超过 Git 或网络的默认限制，可能触发错误。
4. **本地缓存或配置限制**：Git 默认的缓存大小（`http.postBuffer`）较小，大文件传输时可能溢出。

### **解决方法**

#### 1. **切换到 HTTP1.1 协议**

HTTP2 兼容性问题是常见诱因，可强制 Git 使用 HTTP1.1 规避：


```bash
# 全局设置使用 HTTP1.1
git config --global http.version HTTP/1.1

# 再次尝试克隆
git clone https://github.com/excalidraw/excalidraw.git
```

#### 2. **增大 Git 传输缓存**

若因缓存不足导致失败，可增大 `http.postBuffer`（单位：字节，以下示例设为 500MB）：


```bash
git config --global http.postBuffer 524288000  # 500*1024*1024

# 重试克隆
git clone https://github.com/excalidraw/excalidraw.git
```

#### 3. **浅克隆（仅获取最新版本）**

若不需要完整的历史提交记录，可使用浅克隆减少数据传输量：


```bash
git clone --depth 1 https://github.com/excalidraw/excalidraw.git
```

（`--depth 1` 表示只克隆最近 1 次提交，速度更快，适合快速试用）

#### 4. **检查网络连接**

- 确保网络稳定，可尝试重启路由器或切换网络（如从 Wi-Fi 切换到有线网络）。
- 若使用代理或 VPN，暂时关闭后重试（部分代理可能干扰 HTTP2 传输）。
- 测试 GitHub 访问速度：`ping github.com` 或 `curl -I https://github.com`，确认网络通畅。

#### 5. **清理 Git 缓存并重试**

若本地缓存损坏，可清理后重试：

```bash
git config --global --unset http.proxy  # 清除可能的代理配置
git config --global --unset https.proxy

# 重新克隆
git clone https://github.com/excalidraw/excalidraw.git
```

#### 6. **使用 SSH 协议克隆（若有权限）**

若 HTTP 协议持续失败，可尝试 SSH 协议（需提前配置 GitHub SSH 密钥）：


```bash
git clone git@github.com:excalidraw/excalidraw.git
```

### **总结**

优先尝试 **切换 HTTP1.1** 或 **浅克隆**，这两种方法对解决 HTTP2 相关错误最有效。若问题持续，检查网络环境或联系网络管理员排查防火墙 / 代理限制。
