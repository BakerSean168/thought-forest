---
tags:
  - tech/ai/ml
  - type/concept
  - status/seed
description: vmess-to-yaml
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# vmess-to-yaml

## 问题详情

vmess://eyJ2IjoyLCJwcyI6IjIzM2JveS10Y3AtMjAuMjU1Ljk4LjI1NCIsImFkZCI6IjIwLjI1NS45OC4yNTQiLCJwb3J0IjoiMzU3NTgiLCJpZCI6IjRhYjdmNDkzLTcyZGUtNDJhMi1hNDZjLTNhYjZhYTQxMjYxZiIsImFpZCI6IjAiLCJuZXQiOiJ0Y3AiLCJ0eXBlIjoibm9uZSIsInBhdGgiOiIifQ==

选择: Shadowsocks-40959.json
-------------- Shadowsocks-40959.json -------------
协议 (protocol) = shadowsocks
地址 (address) = 20.255.98.254
端口 (port) = 40959
密码 (password) = cc832bec-4ef0-4a9f-aca8-f7b8e0f238e1
加密方式 (encryption) = chacha20-ietf-poly1305
------------- 链接 (URL) -------------
ss://Y2hhY2hhMjAtaWV0Zi1wb2x5MTMwNTpjYzgzMmJlYy00ZWYwLTRhOWYtYWNhOC1mN2I4ZTBmMjM4ZTE=@20.255.98.254:40959#233boy-ss-20.255.98.254

选择: VMess-QUIC-28595.json
-------------- VMess-QUIC-28595.json -------------
协议 (protocol) = vmess
地址 (address) = 20.255.98.254
端口 (port) = 28595
用户ID (id) = 40c5732c-15f5-4660-a69c-6227f67e0ccf
传输协议 (network) = quic
伪装类型 (type) = dtls
------------- 链接 (URL) -------------
vmess://eyJ2IjoyLCJwcyI6IjIzM2JveS1xdWljLTIwLjI1NS45OC4yNTQiLCJhZGQiOiIyMC4yNTUuOTguMjU0IiwicG9ydCI6IjI4NTk1IiwiaWQiOiI0MGM1NzMyYy0xNWY1LTQ2NjAtYTY5Yy02MjI3ZjY3ZTBjY2YiLCJhaWQiOiIwIiwibmV0IjoicXVpYyIsInR5cGUiOiJkdGxzIiwicGF0aCI6IiJ9

怎么转为 clash 可使用的 yaml 文件

## 解决方案

vmess 后缀使用 base64 加密，里面就是很多信息，填写到 文件中即可。规则填写是个问题

```yaml
proxies:
  - name: "233boy-tcp-20.255.98.254"  # 从链接的ps字段获取
    type: vmess
    server: 20.255.98.254  # 服务器地址（add字段）
    port: 35758  # 端口（port字段）
    uuid: 4ab7f493-72de-42a2-a46c-3ab6aa41261f  # 认证UUID（id字段）
    alterId: 0  # 额外ID（aid字段）
    cipher: auto  # 加密方式（链接中未指定，默认auto）
    tls: false  # 链接中未启用TLS（tls字段缺省）
    network: tcp  # 传输协议（net字段）
    ws-opts: {}  # 因协议为tcp，WebSocket相关配置留空
    # 其他可选配置（根据实际需求添加）
    # udp: true
    # servername: example.com
    # skip-cert-verify: false

proxy-groups:
  - name: Proxy  # 示例代理组
    type: select
    proxies:
      - "233boy-tcp-20.255.98.254"  # 引用上面的代理名称

rules:
  - DOMAIN-SUFFIX,google.com,Proxy  # 示例规则（按需添加）
  - DOMAIN-SUFFIX,github.com,Proxy
  - GEOIP,CN,DIRECT  # 国内地址直连
```

## 实战经验

## 经验总结

## 信息参考
