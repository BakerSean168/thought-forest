---
tags:
  - tech/dev/network
  - type/howto
  - status/seed
description: wsl中主机代理导致的网络问题
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[计算机网络 MOC]] | [[前端基础 MOC]]

---


## 问题详情

- 现象：在 WSL 中运行 `sudo apt update` / `sudo apt install ...` 时，出现 “Could not get lock /var/lib/apt/lists/lock”（偶发）、以及大量 “Failed to fetch … Connection timed out / Connection failed” 之类的网络错误，导致 apt 无法正常更新索引或定位包（例如最初找不到 postgresql-16）。
- 背景网络：宿主机（Windows）上运行了 Clash（或类似的本地代理/工具），并在系统或用户环境中配置了代理地址（本例中是 127.0.0.1:7890）。WSL 内的环境变量中有：

  ```
  http_proxy=http://127.0.0.1:7890
  https_proxy=http://127.0.0.1:7890
  HTTP_PROXY=...
  HTTPS_PROXY=...
  no_proxy=...
  ```

- 直接症状与表现：
  - 部分通过直接 IPv4 访问的 apt 请求会超时（某些 CDN/mirror 的 IP 在你的网络路径上不可达），导致 apt 报“Failed to fetch”。
  - 有时工具（curl/apt）通过本地代理可成功访问同一资源；但 apt 并不恒定地使用系统环境变量或会在尝试直连某些 IP（比如优先尝试某个 CDN IP）时失败。
  - postgresql.list 还有一个格式问题（URL 被换行），这会导致 apt 无法识别源；这个是独立但被同时发现并修复了。

## 怎么导致的（技术细节）

- 代理与解析/路由交互：
  - Clash 在宿主机上提供本地代理和/或 DNS 转发。WSL 在默认配置下可以通过本地转发访问 Windows 主机的 127.0.0.1，因而 WSL 内指向 `127.0.0.1:7890` 的代理是可达的。
  - DNS/CDN 负载均衡会为同一域名返回多个 A/AAAA（多 IP）。有些 IP 通过你的网络路径不可达（路由被 ISP、企业网络、或地域节点限制），导致直接访问这些 IP 的请求超时。
  - 如果进程没有正确走代理（或代理不负责某类流量/IPv6），就会出现连接超时；而当你通过代理访问时，代理会从宿主机发起请求，避开那些本地对某些 IP 的路由问题，从而成功。
- apt 的特殊点：
  - apt 在不同情况下可能尝试 IPv4、IPv6 或直接连接某些 IP；并且 apt 有自己的网络细节（不像 curl 那样总是尊重 shell 环境变量），因此需要明确配置 apt 使用代理（或用 apt 命令行参数指定）。
  - apt 还对仓库安全性严格检查（Release/InRelease 文件）。如果仓库在某些连接方式下不可达，apt 会报错并忽略部分索引。
- 额外问题（在本次会话里也发现）：
  - 源文件格式错误（postgresql.list 中 URL 被换行）会导致 apt 无法定位包，即使网络本身可达也会找不到该包。这个是独立的配置问题，已被修正。

## 解决方案（操作步骤与命令）

下面按优先级列实操步骤（包含你可以直接复制粘贴的命令）。

1) 临时绕过 / 验证：用 apt 的命令行参数让 apt 通过本地代理（用于马上能成功 update/install）

```bash
sudo apt -o Acquire::http::Proxy="http://127.0.0.1:7890" \
         -o Acquire::https::Proxy="http://127.0.0.1:7890" update
sudo apt -o Acquire::http::Proxy="http://127.0.0.1:7890" \
         -o Acquire::https::Proxy="http://127.0.0.1:7890" install -y postgresql-16 postgresql-client-16
```

- 说明：这在本次会话中已被证明有效（成功下载并安装 PostgreSQL）。

2) 持久化 apt 的代理配置（如果你希望 apt 长期走宿主代理）

```bash
# 将此文件创建为 root
sudo tee /etc/apt/apt.conf.d/95proxy > /dev/null <<'EOF'
Acquire::http::Proxy "http://127.0.0.1:7890";
Acquire::https::Proxy "http://127.0.0.1:7890";
EOF
```

- 风险/注意：如果你以后关闭宿主代理或代理端口变更，apt 将无法联网，记得同时移除或更新该文件。

3) 如果你不想让 WSL 继承宿主代理（更严格/不想用代理）
- 查找并移除 shell 配置或服务里设置的代理环境变量（例如 `~/.bashrc`, `/etc/profile.d/*`，或 Windows 到 WSL 的 env 传递），或在 WSL 内取消导出：

```bash
# 临时取消当前 shell 的代理变量（关闭后重新打开终端恢复）
unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY
# 永久：编辑对应的 shell 初始化文件（比如 ~/.bashrc），删除或注释 export lines
```

- 注意：如果宿主机或公司网络必须使用代理才能访问外网，则移除可能造成更普遍的网络不可达。

4) DNS 稳定性（可选但建议）
- WSL 默认会自动生成 resolv.conf 指向 Windows 的 DNS（例如 `nameserver 10.255.255.254`）。在某些场景你想固定 DNS：

```bash
# 在 WSL 中禁用自动生成 resolv.conf，并写入你信任的 DNS（例如 114.114.114.114）
echo -e "[network]\ngenerateResolvConf = false" | sudo tee /etc/wsl.conf
sudo rm -f /etc/resolv.conf
sudo tee /etc/resolv.conf > /dev/null <<'EOF'
nameserver 114.114.114.114
nameserver 223.5.5.5
EOF
# 然后在 Windows PowerShell/命令行运行：
# wsl --shutdown
# 之后重启 WSL 实例（重新打开 Ubuntu 等）
```

- 说明：禁用自动生成会断开 WSL 自动继承 Windows DNS 的行为；如果你需要内部域名解析或公司内部 DNS，请不要禁用，或在自定义 resolv.conf 中添加相应 nameserver。

5) 检查并修复 apt 源文件格式问题（我们已修复）
- 如果遇到包找不到，检查文件：

```bash
sudo sed -n '1,200p' /etc/apt/sources.list.d/postgresql.list
# 期望看到单行类似：
# deb [arch=amd64,arm64,ppc64el signed-by=/usr/share/keyrings/postgresql.gpg] https://apt.postgresql.org/pub/repos/apt/ noble-pgdg main
```

6) 诊断命令（用于复现或调查）
- 查看代理环境：

```bash
env | egrep -i 'proxy' || true
```

- 查看 /etc/resolv.conf：

```bash
cat /etc/resolv.conf
```

- 解析与访问测试：

```bash
getent hosts mirrors.aliyun.com
ping -c3 mirrors.aliyun.com
curl -I --connect-timeout 10 http://mirrors.aliyun.com
curl -I --connect-timeout 10 https://apt.postgresql.org/pub/repos/apt/dists/noble-pgdg/InRelease
```

- 强制 apt 使用代理并 update（见上文）；
- 查锁与进程（如果出现 lock 错误）：

```bash
ps aux | egrep 'apt|dpkg' | egrep -v egrep
sudo fuser -v /var/lib/apt/lists/lock || true
ls -l /var/lib/apt/lists/lock
```

## 实战经验（来自本次诊断）

- 证据记录：
  - 我们在会话中发现 `http_proxy`/`https_proxy` 环境变量被设置为 `http://127.0.0.1:7890`，curl 访问同一 URL 时显示 remote_ip 为 127.0.0.1（说明请求被代理/本地代理参与）。apt 直接请求某些 CDN IP 时出现超时，但通过代理则成功。
  - 修复 `postgresql.list` 的换行后，apt 能识别源；但仍出现一些连接超时，直到显式让 apt 使用本地代理，才成功下载索引并安装包。
- 教训与技巧：
  - 遇到 apt 网络问题时，先检查是否有代理环境或公司网络限制（proxy、VPN、split-tunnel）。代理既可能是原因，也可能是解决办法（通过代理可以跨过某些网络限制）。
  - apt 的行为与 curl/wget 并不完全相同：不要仅凭 curl 成功就认为 apt 会成功，建议直接临时用 `-o Acquire::http::Proxy=...` 命令参数去验证 apt。
  - 多 IP 的 CDN 解析会让问题更难复现：同一个域名不同时间/不同 DNS 可能返回不同 IP，某些 IP 可达、某些不可达，因此重试与代理有时能立即解决问题。

## 经验总结（短条）

- 问题根本在于“代理/网络路径不一致” + “apt 与系统环境的交互差异”。Clash（宿主代理）既可能带来访问能力（当 apt 使用代理时），也可能因环境未统一而导致间歇性失败（当 apt尝试直连或优先某些 IP 时）。
- 最短可行修复：让 apt 显式使用可用的本地代理（或修正/移除环境变量），并修正任何源文件格式错误。
- 长期可维护性：决定好是否依赖宿主代理（若依赖，持久化 apt proxy 配置并监控宿主代理的可用性；若不依赖，则从 shell/profile 里移除代理设置并确保 WSL DNS 与网络直接可达）。

## 信息参考（快速链接与关键词）

- apt proxy 配置：Search "APT proxy configuration apt.conf.d Acquire::http::Proxy"
- WSL DNS & resolv.conf：Search "WSL resolv.conf generateResolvConf"
- Clash/Clash for Windows：代理与 DNS 模式说明（TUN/DNS/HTTP/SOCKS 模式差异）
- apt-secure / Release vs InRelease：Search "apt InRelease Release apt-secure(8)"

如果你希望，我可以现在为你做一件事（选一项直接执行）：
1) 为 `apt` 持久化写入代理配置文件 `/etc/apt/apt.conf.d/95proxy`（我会创建并展示内容）。  
2) 在你的 WSL 中禁用自动生成 `resolv.conf` 并写入固定 DNS（我会创建 wsl.conf + resolv.conf 并提醒你在 Windows 端运行 `wsl --shutdown`）。  
3) 清理所有代理环境变量（临时或永久）并重新验证 `sudo apt update`（适用于你想脱离宿主代理的情况）。  
4) 不做更改，只保存这份总结。

告诉我你要我立刻执行哪项（1/2/3/4），我就马上执行并回报结果。
