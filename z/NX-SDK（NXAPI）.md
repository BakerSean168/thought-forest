---
tags:
  - tech/ops/network
  - type/concept
  - status/growing
description: Cisco NX-OS SDK与NXAPI编程接口
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[网络设备 MOC]] | [[Nexus9000v]]

---


## NX-SDK（NX-API） — Python 使用指南

下面补充一节面向 Python 开发者的 NX-API 使用指南，包含一个小型客户端封装示例、重试/超时与 SSL 处理建议、幂等与回滚模式示例，以及单元/集成测试建议。

### 概要

在 Python 中直接使用 `requests` 调用 NX-API 是最常见的做法。为降低重复代码并统一错误处理，建议封装一个轻量的客户端类，提供 `show`（只读）和 `config`（配置）两类方法，并在上层实现重试、超时、日志与回滚策略。

### 环境与依赖

- Python 3.8+
- requests（已在 `requirements.txt`）
- 可选：`urllib3` 的 `Retry` 或 `tenacity` 用于更灵活的重试策略

示例安装：

```powershell
pip install requests
# 可选的重试库
pip install tenacity
```

### 推荐的轻量 `NXAPIClient`（示例）

下面是一个同步、基于 `requests.Session` 的示例客户端：

```python
import json
import logging
from typing import List, Optional, Tuple
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

logger = logging.getLogger(__name__)

class NXAPIClient:
  def __init__(self, url: str, username: str, password: str,
         timeout: int = 10, verify: bool = True, cert: Optional[str] = None,
         max_retries: int = 3):
    self.url = url
    self.auth = (username, password)
    self.timeout = timeout
    self.verify = cert if cert is not None else verify
    self.session = requests.Session()

    # Configure retries for idempotent requests
    retries = Retry(total=max_retries,
            backoff_factor=0.5,
            status_forcelist=(500, 502, 503, 504),
            allowed_methods=frozenset(['GET', 'POST']))
    self.session.mount('https://', HTTPAdapter(max_retries=retries))
    self.session.mount('http://', HTTPAdapter(max_retries=retries))

  def _post(self, payload, expect_list: bool = True):
    data = json.dumps(payload)
    try:
      resp = self.session.post(self.url,
                   auth=self.auth,
                   headers={'Content-Type': 'application/json-rpc'},
                   data=data,
                   timeout=self.timeout,
                   verify=self.verify)
      resp.raise_for_status()
      return resp.json()
    except requests.exceptions.RequestException as e:
      logger.exception("NX-API request failed")
      raise

  def run_show(self, cmd: str):
    payload = {"jsonrpc": "2.0", "method": "cli", "params": {"cmd": cmd, "version": 1}, "id": 1}
    return self._post([payload])

  def run_config(self, commands: List[str]) -> Tuple[bool, List]:
    payload = []
    for i, cmd in enumerate(commands, 1):
      payload.append({
        "jsonrpc": "2.0",
        "method": "cli_conf",
        "params": {"cmd": cmd, "version": 1},
        "id": i
      })
    results = self._post(payload)
    # 返回 (success, results)
    if isinstance(results, list):
      for r in results:
        if 'error' in r:
          return False, results
      return True, results
    elif 'error' in results:
      return False, [results]
    return True, [results]

  def snapshot_running_config(self) -> Optional[str]:
    # 获取运行配置的文本快照（示例）
    res = self.run_show('show running-config')
    # 尽量从返回中提取 body 或文本部分
    return json.dumps(res)

  def rollback_from_snapshot(self, snapshot_text: str) -> bool:
    # 回滚示例：把 snapshot_text 拆成命令并下发
    # 注意：实际回滚需要解析并取出必要的命令片段
    # 这里给出一个占位说明
    raise NotImplementedError('Rollback needs project-specific logic')

```

### SSL / 证书处理建议

- 实验环境常用 `verify=False` 或接受自签名证书，但**切勿**在生产中长期使用 `verify=False`。
- 推荐做法：把受信任的 CA 或交换机证书放在控制机上，传 `cert='/path/to/ca_bundle.pem'` 给 `NXAPIClient` 的 `verify` 参数。
- 如果必须跳过验证，务必在日志和运维文档中记录风险，并限制客户端来源IP。

### 重试与退避策略

- 使用 `urllib3.Retry`（示例中已配置）或 `tenacity` 实现带指数退避的重试。
- 对配置类命令（`cli_conf`）请谨慎重试，因为重复执行可能具有副作用。推荐：
  - 先在脚本层实现幂等检测（如判断某个对象是否已存在），再决定是否重试。

### 幂等性与回滚模式（示例流程）

1. 读取并保存受影响对象的当前状态（快照）：相关 ACL、class-map、policy-map、接口配置等。
2. 生成要下发的命令，进行本地静态校验（命名冲突、语法检查、影响范围）。
3. 下发变更（分批、分阶段），每步都检查返回值；若发现错误，触发回滚流程。
4. 回滚：根据事先保存的快照把配置写回或下发相应的 `no ...` 命令。

示例伪代码：

```python
client = NXAPIClient(url, user, pwd, verify=False)

# 1. snapshot
snapshot = client.snapshot_running_config()

try:
  success, res = client.run_config(commands)
  if not success:
    # 解析失败并回滚
    client.rollback_from_snapshot(snapshot)
except Exception:
  client.rollback_from_snapshot(snapshot)
  raise
```

### 并发与节流

- 当要对多台交换机或对单台设备进行大量命令时，使用并发库（`concurrent.futures.ThreadPoolExecutor`）配合限速以避免压垮管理平面。
- 对同一设备的并发写操作要特别小心，务必串行化或使用分批锁。

### 单元测试与集成测试建议

- 单元测试：使用 `unittest.mock` 或 `responses` 库 mock `requests.Session.post`，验证客户端在不同响应下的行为（成功、HTTP错误、API内部error字段）。
- 集成测试：在受控的实验环境中运行 `connection_test.py`、`qos_api_explorer.py` 验证端到端流程。建议使用一个专门的测试交换机实例或容器化的 Nexus 9000v。

示例单元测试（伪代码）：

```python
from unittest.mock import patch

@patch('requests.Session.post')
def test_run_show_success(mock_post):
  mock_post.return_value.status_code = 200
  mock_post.return_value.json.return_value = {'result': {'body': {'dummy': 1}}}
  client = NXAPIClient(url, 'u', 'p', verify=False)
  res = client.run_show('show version')
  assert 'result' in res
```

### 实践小贴士

- 先在脚本中实现“dry-run”模式：把要发送的命令打印或写入日志，人工审阅后再启用真实下发。
- 对重要变更，采用蓝绿或分段发布策略：先在一台或一小组交换机上验证，确认正确后再扩展到全网。

---

## 信息参考

[CiscoDevNet/NX-SDK: NX-OS SDK](https://github.com/CiscoDevNet/NX-SDK)
