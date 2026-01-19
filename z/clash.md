---
tags:
  - tech/ops/proxy
  - type/concept
  - status/growing
description: Clash跨平台代理工具基础与配置
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS 资源与综合应用 MOC]] | [[代理节点]]

---




# Clash

Clash 是一个跨平台的基于规则的代理工具, 在网络和应用层运行。

Clash 使用 YAML (YAML Ain't Markup Language) 作为配置文件格式。

## Clash 原理（两个关键部分）：

- Inbound 入站
  Inbound 入站是在本地端监听的部分, 它通过打开一个本地端口并监听传入的连接来工作. 当连接进来时, Clash 会查询配置文件中配置的规则, 并决定连接应该去哪个 Outbound 出站。
- Outbound 出站  
  Outbound 出站是连接到远程端的部分. 根据配置的不同, 它可以是一个特定的网络接口、一个代理服务器或一个策略组。

## 基于规则的路由

Clash 支持基于规则的路由, 这意味着您可以根据各种规则将数据包路由到不同的出站. 规则可以在配置文件的 rules 部分中定义。  
有许多可用的规则类型, 每种规则类型都有自己的语法. 规则的一般语法是:

```bash
# 类型,参数,策略(,no-resolve)
TYPE,ARGUMENT,POLICY(,no-resolve)
```

# 配置

## 配置文件

主配置文件名为 config.yaml. 默认情况下, Clash 会在 $HOME/.config/clash 目录读取配置文件.

-d 指定配置目录  
-f 指定配置文件

## 特殊语法

### IPv6 地址

用方括号（[]）包裹 IPv6 地址

### DNS 通配符域名匹配

任何包含这些字符的域名都应该用单引号 (') 包裹. 例如, '_.google.com'. 静态域名的优先级高于通配符域名 (foo.example.com > _.example.com > .example.com) .

使用星号 (*) 来匹配单级通配符子域名  
使用点号 (.) 来匹配多级通配符子域名（不包括根域名）  
使用加号 (+) 来匹配多级通配符子域名（包括根域名）

# Rules 规则

[Clash.wiki.Rules](https://clash.wiki/configuration/rules.html)

## 策略

目前有四种策略类型,:

- DIRECT: 通过 interface-name 直接连接到目标 (不查找系统路由表)
- REJECT: 丢弃数据包
- Proxy: 将数据包路由到指定的代理服务器
- Proxy Group: 将数据包路由到指定的策略组

## 规则

| 规则类型       | 语法示例                                | 说明                   |
| -------------- | --------------------------------------- | ---------------------- |
| DOMAIN         | `DOMAIN,www.google.com,policy`          | 精确匹配域名           |
| DOMAIN-SUFFIX  | `DOMAIN-SUFFIX,youtube.com,policy`      | 匹配域名后缀           |
| DOMAIN-KEYWORD | `DOMAIN-KEYWORD,google,policy`          | 匹配域名关键字         |
| GEOIP          | `GEOIP,CN,policy`                       | 匹配 IP 地理位置       |
| IP-CIDR        | `IP-CIDR,127.0.0.0/8,DIRECT`            | 匹配 IPv4 地址段       |
| IP-CIDR6       | `IP-CIDR6,2620:0:2d0:200::7/32,policy`  | 匹配 IPv6 地址段       |
| SRC-IP-CIDR    | `SRC-IP-CIDR,192.168.1.201/32,DIRECT`   | 匹配源 IP 地址段       |
| SRC-PORT       | `SRC-PORT,80,policy`                    | 匹配源端口             |
| DST-PORT       | `DST-PORT,80,policy`                    | 匹配目标端口           |
| PROCESS-NAME   | `PROCESS-NAME,nc,DIRECT`                | 匹配进程名称           |
| PROCESS-PATH   | `PROCESS-PATH,/usr/local/bin/nc,DIRECT` | 匹配进程路径           |
| IPSET          | `IPSET,chnroute,policy`                 | 匹配 IP 集合(仅 Linux) |
| RULE-SET       | `RULE-SET,my-rule-provider,DIRECT`      | 匹配规则集(仅 Premium) |
| SCRIPT         | `SCRIPT,script-path,DIRECT`             | 执行脚本(仅 Premium)   |
| MATCH          | `MATCH,policy`                          | 全匹配(最后规则)       |

# Clash DNS

由于 Clash 的某些部分运行在第 3 层 (网络层) , 因此其数据包的域名是无法获取的, 也就无法进行基于规则的路由.  
Enter fake-ip: 它支持基于规则的路由, 最大程度地减少了 DNS 污染攻击的影响, 并且提高了网络性能, 有时甚至是显著的.  

## fake-ip

"fake IP" 的概念源自 RFC 3089:  
  一个 "fake IP" 地址被用于查询相应的 "FQDN" 信息的关键字.  
fake-ip 池的默认 CIDR 是 198.18.0.1/16 (一个保留的 IPv4 地址空间, 可以在 dns.fake-ip-range 中进行更改).  

当 DNS 请求被发送到 Clash DNS 时, Clash 内核会通过管理内部的域名和其 fake-ip 地址的映射, 从池中分配一个 空闲 的 fake-ip 地址.  
以使用浏览器访问 http://google.com 为例：  
1. 浏览器向 Clash DNS 请求 google.com 的 IP 地址  
2. Clash 检查内部映射并返回 198.18.1.5  
3. 浏览器向 198.18.1.5 的 80/tcp 端口发送 HTTP 请求  
4. 当收到 198.18.1.5 的入站数据包时, Clash 查询内部映射, 发现客户端实际上是在向 google.com 发送数据包  
5. 根据规则的不同:  
  Clash 可能仅将域名发送到 SOCKS5 或 shadowsocks 等出站代理, 并与代理服务器建立连接  
  或者 Clash 可能会基于 SCRIPT、GEOIP、IP-CIDR 规则或者使用 DIRECT 直连出口查询 google.com 的真实 IP 地址  

## 实战经验

[[vmess-to-yaml]]

### 添加节点


```yaml
proxies:
  # 节点1
  - name: "tcp"
    type: vmess
    server: [ip-address]
    port: 30
    uuid: b2028ac
    alterId: 0
    cipher: auto
    tls: false
    network: tcp
    ws-opts: {}
    udp: false

  # 节点2
  # - name: "quic"
  #   type: vmess
  #   server: 2.5.8.54
  #   port: 370
  #   uuid: 40e0ccf
  #   alterId: 0
  #   cipher: auto
  #   tls: false
  #   network: quic
  #   ws-opts: {}
  #   udp: false

  # 节点3
  - name: "quic"  # 节点名称（取自 ps 字段）
    type: vmess  # 协议类型为 VMess
    server: 20.2.9.4  # 服务器地址（取自 add 字段）
    port: 28595  # 端口（取自 port 字段，转换为整数）
    uuid: 40c0ccf  # 用户 ID（取自 id 字段）
    alterId: 0  # 额外 ID（取自 aid 字段，转换为整数）
    cipher: auto  # 加密方式（默认 auto，兼容大多数节点）
    tls: false  # QUIC 通常自带加密，无需额外启用 TLS（按节点实际配置调整）
    network: quic  # 传输协议（取自 net 字段）
    quic-opts:  # QUIC 协议专属配置
      security: dtls  # 加密/伪装类型（取自 type 字段）
      key: ""  # 若节点有密钥可填写（此处 path 为空，暂不填）
      # 可选：若节点绑定域名，可添加 servername
      # servername: example.com
```

### 添加节点组

```yaml
proxy-groups:
  - name: Proxy
    type: select
    proxies:
      - "tcp"
      - "quic"
      - DIRECT
  
  - name: Ban
    type: select
    proxies:
      - REJECT  # Clash 内置的拒绝动作，直接阻断流量
      - DIRECT
```

### 通过url获取规则

```yaml
rule-providers:
  AdvertisingLite:
    type: http  # 远程 HTTP 方式获取
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/refs/heads/master/rule/Clash/AdvertisingLite/AdvertisingLite_Classical.yaml"  # 替换为实际规则文件 URL
    interval: 86400  # 每天更新一次（单位：秒）
    path: ./rules/AdvertisingLite.yaml  # 本地缓存路径
    behavior: classical  # 按传统规则格式解析（必须添加，否则可能无法识别）
```

### 直接嵌入规则

```yaml
rules:
  # -------------------------- 1. Microsoft 基础服务（直连，不影响Copilot）--------------------------
  - DOMAIN-SUFFIX,windows.com,DIRECT
  - DOMAIN-SUFFIX,microsoft.com,DIRECT  # 基础微软服务直连（Copilot单独规则覆盖）
  - DOMAIN-SUFFIX,msft.net,DIRECT
  - DOMAIN-SUFFIX,live.com,DIRECT
  - DOMAIN-SUFFIX,xbox.com,DIRECT
  - DOMAIN-SUFFIX,office.com,DIRECT
  - DOMAIN-SUFFIX,onenote.com,DIRECT
  - DOMAIN-SUFFIX,outlook.com,DIRECT
  - DOMAIN-SUFFIX,skype.com,DIRECT

  # -------------------------- 2. VS Code Copilot 专属规则（全部走代理）--------------------------
  # Copilot 核心API域名（模型请求、代码补全）
  - DOMAIN-SUFFIX,githubcopilot.com,Proxy  # Copilot 主服务域名
  - DOMAIN-SUFFIX,copilot-proxy.githubusercontent.com,Proxy  # Copilot 代理服务器
  - DOMAIN-SUFFIX,api.githubcopilot.com,Proxy  # Copilot API接口
  - DOMAIN-SUFFIX,models.githubcopilot.com,Proxy  # Copilot 模型服务
  # 关联的GitHub服务（Copilot依赖GitHub登录和授权）
  - DOMAIN-SUFFIX,githubusercontent.com,Proxy  # GitHub 资源托管（含Copilot配置）
  - DOMAIN-SUFFIX,ghcr.io,Proxy  # GitHub 容器 registry（Copilot相关镜像）
  # 微软Azure支持的Copilot后端（部分模型部署在Azure）
  - DOMAIN-SUFFIX,azure.com,Proxy  # 仅覆盖Copilot相关的Azure服务（不影响国内Azure）
  - DOMAIN-SUFFIX,azureedge.net,Proxy  # Azure CDN（Copilot模型加速）

  # -------------------------- 3. 国内基础规则--------------------------
  - GEOIP,CN,DIRECT
  - DOMAIN-SUFFIX,cn,DIRECT
  - DOMAIN-KEYWORD,.中国.,DIRECT
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT

  # -------------------------- 4. 其他境外服务（走代理）--------------------------
  - DOMAIN-SUFFIX,reddit.com,Proxy
  - DOMAIN-SUFFIX,redd.it,Proxy
  - DOMAIN-SUFFIX,stackoverflow.com,Proxy
  - DOMAIN-SUFFIX,stackexchange.com,Proxy
  - DOMAIN-SUFFIX,google.com,Proxy
  - DOMAIN-SUFFIX,github.com,Proxy
  - DOMAIN-SUFFIX,youtube.com,Proxy
  - DOMAIN-SUFFIX,twitter.com,Proxy
  - DOMAIN-SUFFIX,facebook.com,Proxy

  # -------------------------- 5. 兜底规则--------------------------
  - MATCH,DIRECT
```

### 代理组嵌套代理组

**使用场景**：
有了某一类（比如Microsoft）的匹配规则，希望能手动控制是 哪一个代理，此时可以直接添加具体的节点，也可以使用别的代理组的使用节点（Proxy 节点组当前使用哪个节点就使用哪个）

```yaml
proxy-groups:
  - name: Proxy
    type: select
    proxies:
      - "tcp"
      - "quic"
      - DIRECT

  - name: Microsoft-proxy
    type: select
    proxies:
      - Proxy
      - DIRECT
```

# 客户端使用

## 更新

以下的更新都是分开的：
- 配置文件更新(file)
- 外部资源更新(provider)
- GUI软件更新
- mihomo内核更新


> [!NOTE] 注意
> 所以当使用 provider 提供节点时，修改了节点信息（名字、配置、地址）后，应该通过外部资源更新来获取，使用配置文件更新是没有用的！


## 相关资源

转换：
[v2ray转clash节点 - v2rayse.com](https://v1.v2rayse.com/v2ray-clash/)
[ACL4SSR 在线订阅转换](https://acl4ssr-sub.github.io/)

规则：
[ios_rule_script/rule/Clash at master · blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Clash)

## 参考

信息来源：[Clash 知识库](https://clash.wiki/)
