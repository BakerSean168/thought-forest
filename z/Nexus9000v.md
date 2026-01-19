---
tags:
  - tech/ops/network
  - type/concept
  - status/seed
description: Cisco Nexus 9000v虚拟交换机
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[网络设备 MOC]] | [[DevOps MOC]]

---


# Nexus9000v

## 概要

本文档面向网络工程师与开发人员，旨在提供 Cisco Nexus 9000v 的基础概念、常用操作指南、与在 SDN/自动化场景下的实战经验与要点。包含 NX-API 使用注意事项、常见故障排查步骤与实用命令示例，便于在校园网或实验环境中部署与调试。

沙箱免费的测试环境[[Cisco-DevNet-Sandbox]]

---

## 基础概念

### 设备与平台

- Nexus 9000v 是 Cisco 提供的虚拟交换机平台（以软件形式运行），常用于实验、开发或测试环境。
- 运行模式：NX-OS (软件版本)；可具备多种特性（L2/L3、QoS、ACL、VLAN、VXLAN等）。

### 管理接口

- 管理平面常见方式：Console、SSH、HTTP/HTTPS（NX-API）、NETCONF/RESTCONF（取决于版本和许可）。
- NX-API：Cisco 为 NX-OS 提供的 HTTP(S)-based JSON-RPC 接口，适合自动化脚本调用，能够执行 `show`（只读）或 `cli_conf`（配置）命令。

### 证书与加密

- NX-API 支持 HTTPS，但在虚拟或实验环境中往往使用自签名证书或直接配置为 HTTP（不推荐生产）。
- 在生产或校网场景，请配置受信任的证书并强制 HTTPS，避免凭证和配置命令在网络中明文传输。

### 命令与模式

- 交互式命令分为 `exec`（show）与 `config`（配置）模式，NX-API 的 `method` 字段通常为 `cli` 或 `cli_conf`。

---

## 使用指南

### 访问准备

1. 启用 NX-API（如尚未启用）：

```
configure terminal
feature nxapi
copy running-config startup-config
```

2. 检查管理接口的连通性（从控制主机）：

```powershell

# curl 测试（HTTP）

curl -v http://<SWITCH_IP>/ins

  

# curl 测试（HTTPS，跳过证书验证）

curl -k https://<SWITCH_IP>/ins

```

3. 推荐在 `.env` 或机密存储中管理用户名/密码，避免放在脚本中明文。

### 常用 NX-API 示例（Python requests）

- 只读命令（show）：

```python

payload = {
  "jsonrpc": "2.0",
  "method": "cli",
  "params": {"cmd": "show interface brief", "version": 1},
  "id": 1
}
res = requests.post(url, auth=(user, pwd), headers=headers, data=json.dumps([payload]), verify=False)
```

- 配置命令（cli_conf）：

```python

payload = [
  {"jsonrpc":"2.0", "method":"cli_conf", "params":{"cmd":"vlan 100", "version":1}, "id":1},
  {"jsonrpc":"2.0", "method":"cli_conf", "params":{"cmd":"name VLAN100", "version":1}, "id":2}
]

res = requests.post(url, auth=(user, pwd), headers=headers, data=json.dumps(payload), verify=False)

```

> 注意：某些 NX-OS 版本对一次性批量 `cli_conf` 的命令数量或顺序有要求，必要时分多次发送并检查返回结果。

### 常见配置示例

- 创建 VLAN：

```
configure terminal
vlan 100
name STUDENT_NET
exit
```

- 在接口上配置 QoS 策略示例：

```
configure terminal
ip access-list ACL_COURSE
  10 permit ip 10.1.1.10/32 any
class-map type qos CM_COURSE
  match access-group name ACL_COURSE
policy-map type qos PM_COURSE
  class CM_COURSE
    set dscp af41
interface Ethernet1/1
  service-policy type qos input PM_COURSE
exit
```

---

## 实战经验（在 SDN / 自动化 场景）

### 1) 开发与调试建议

- 从只读 `show` 命令开始验证连通性，确认 JSON 返回格式后再执行 `cli_conf`。
- 在虚拟环境中优先使用 HTTP 方便调试（但要注意安全）；生产环境必须使用 HTTPS 并配置受信任证书。
- 使用 `timeout` 与 `retry` 逻辑，避免因为短暂网络问题导致脚本失败。

### 2) 命令幂等性与回滚

- 自动化脚本应尽量保证幂等（idempotent），例如使用 `vlan 100`（重复执行无害）
- 对于不可逆或会覆盖现有策略的操作，先读取并保存当前配置（快照），在失败时回滚：

  - 方案A：在发送配置前使用 `show running-config` 保存相关片段；失败则把原配置写回。

  - 方案B：将变更封装为可逆命令序列（先应用，再在完成确认后删除备份）。

### 3) 策略冲突检测

- 在下发重要QoS或ACL策略前，先查询现有 class-map / policy-map / ACL，检测命名冲突或匹配范围重叠。
- 维护策略命名约定（例如 `PM_<policy_name>`），便于清理与审计。

### 4) 安全与审计

- 将操作日志化：记录谁（用户/服务）在何时对哪台设备下发了哪些命令以及返回结果。
- 对高权限API访问限制IP白名单或使用基于角色的访问控制（RBAC），并且使用API令牌或证书验证。

### 5) 性能与可用性

- 批量变更（如同时在多台设备上下发）时，采用并发但有速率限制的方式，以避免对交换机管理平面造成压力。
- 避免在高峰时段对关键接口或策略做大规模修改。

---

## 经验总结

- 对于快速验证与实验环境，Nexus 9000v + NX-API 是高效的选择。
- 自动化策略下发必须考虑幂等性、回滚与冲突检测，否则容易产生网络中断或策略覆盖问题。
- 在校园网等生产环境中，安全（HTTPS、证书、审计）永远是首要考虑。
- 先做小范围验证，再放大到批量下发；并确保有监控与可回滚方案。

---

## 信息参考

- Cisco NX-OS and NX-API 文档（Cisco 官方）
- NX-API JSON-RPC 接口说明
- Cisco CLI 命令参考手册
- 项目内部 `qos_api_explorer.py`、`connection_test.py`、`engine.py` 供示例参考

---

如果你希望我：

- 把该文档添加到 README 的链接中；
- 或者把内容拆成多个子文件（如 `nxapi.md`, `qos_practices.md`）；
- 或者把某些命令改成可复制的一键脚本（PowerShell / Bash）；

请告诉我，我会继续帮你完善。
