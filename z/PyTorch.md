---
tags:
  - tech/ai/ml
  - type/concept
  - status/growing
description: PyTorch
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# PyTorch

## 基础概念

核心组件：
1. Tensor：多维数组（可在 CPU / CUDA / MPS / XLA）
2. Autograd：基于反向图的自动求导（动态构图）
3. Module：可复用网络层结构（nn.Module）
4. Optimizer：参数更新算法（SGD / Adam / AdamW 等）
5. DataLoader：批量加载与并行预处理
6. Device / dtype：计算设备与数据类型（float16/bfloat16 混合精度）
7. torch.compile：2.x 编译管线（dynamo + aot autograd + inductor）

动态图优点：调试友好、控制流自然；缺点：极端场景下编译优化空间较小（2.x 通过 compile 缓解）。

分布式：DDP（DistributedDataParallel）、FSDP（Fully Sharded）、RPC、Pipeline Parallel、ZeRO 技术路线支持超大模型。

常见文件结构：model.py / dataset.py / train.py / config.py / utils/。

## 使用指南

### 安装
```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

### 基本 Tensor
```python
import torch
x = torch.randn(3,4, device='cuda')
y = torch.ones(3,4, device='cuda')
z = x + y
print(z.shape, z.dtype)
```

### 自动求导
```python
a = torch.tensor(2.0, requires_grad=True)
b = torch.tensor(3.0, requires_grad=True)
c = a * b + b
c.backward()  # ∂c/∂a = b, ∂c/∂b = a+1
print(a.grad, b.grad)
```

### 简单模型
```python
import torch.nn as nn

class MLP(nn.Module):
  def __init__(self, in_dim, hidden, out_dim):
    super().__init__()
    self.net = nn.Sequential(
      nn.Linear(in_dim, hidden),
      nn.ReLU(),
      nn.Linear(hidden, out_dim)
    )
  def forward(self, x):
    return self.net(x)
```

### 训练循环
```python
model = MLP(100, 256, 10).cuda()
opt = torch.optim.AdamW(model.parameters(), lr=1e-3)
criterion = nn.CrossEntropyLoss()

for step, (bx, by) in enumerate(loader):
  bx = bx.cuda(); by = by.cuda()
  logits = model(bx)
  loss = criterion(logits, by)
  opt.zero_grad()
  loss.backward()
  opt.step()
```

### DataLoader 示例
```python
from torch.utils.data import Dataset, DataLoader

class ToyDs(Dataset):
  def __len__(self): return 1000
  def __getitem__(self, idx):
    import torch
    x = torch.randn(100)
    y = torch.randint(0,10,(1,)).item()
    return x, y

loader = DataLoader(ToyDs(), batch_size=32, shuffle=True, num_workers=4, pin_memory=True)
```

### 混合精度训练
```python
scaler = torch.cuda.amp.GradScaler()
for bx, by in loader:
  with torch.cuda.amp.autocast():
    loss = criterion(model(bx.cuda()), by.cuda())
  scaler.scale(loss).backward()
  scaler.step(opt)
  scaler.update()
  opt.zero_grad(set_to_none=True)
```

### torch.compile
```python
model = torch.compile(model)  # 2.x
```

### 检查显存
```python
print(torch.cuda.memory_summary())
```

### 保存与加载
```python
torch.save(model.state_dict(), 'm.pt')
model.load_state_dict(torch.load('m.pt', map_location='cuda'))
```

### 分布式 DDP 基本
```bash
torchrun --nproc_per_node=4 train.py
```

## 实战经验

1. 提升 IO：使用 num_workers≈CPU 核 - 1；开启 pin_memory
2. 减少 CPU -> GPU 传输：预加载/拼接后一次传输大 batch
3. 梯度累积：显存不足时 `accumulate_steps`
4. 梯度裁剪：`torch.nn.utils.clip_grad_norm_`
5. seed 固定：提高可复现性 `torch.manual_seed`
6. profile 工具：`torch.profiler` 分析热点（算子 / 数据 / 同步）
7. FSDP / ZeRO：大模型拆分参数/优化器/梯度节省显存
8. 混合精度：减少显存与提高吞吐（需关注溢出）
9. 检查梯度异常：NaN -> GradScaler / 梯度裁剪 / loss scale 调整
10. 模块化配置（Hydra / OmegaConf）提升实验管理

常见问题：
- DataLoader 死锁：Windows 下需 `if __name__ == '__main__'`
- GPU 利用率低：瓶颈在数据加载或同步（print 触发 sync）
- 梯度为 None：未参与计算 / 条件分支未走 -> 需显式 `retain_graph` 场景
- 内存泄露：保留计算图未释放；循环中存储中间 Tensor 在列表
- 过拟合：正则 / dropout / early stopping / 数据增强

## 经验总结

核心抓手：数据（质量+吞吐）> 模型结构微调；性能优化从 profiler -> 算子融合 -> 混合精度 -> 分布式策略。

调试策略：逐步缩小（极小 batch / 子集数据），关闭随机性验证逻辑正确；开启 anomaly detection 定位梯度错误。

升级方向：Torch 2.x compile，AMP，FSDP，多 GPU / 多节点，ONNX / TensorRT 推理加速。

## 信息参考

- 官方: <https://pytorch.org>
- 文档: <https://pytorch.org/docs/stable/index.html>
- 分布式: <https://pytorch.org/docs/stable/distributed.html>
- 性能指南: <https://pytorch.org/tutorials/recipes/recipes.html>
- profiler: <https://pytorch.org/docs/stable/profiler.html>
- TorchInductor: <https://pytorch.org/get-started/pytorch-2.0/>

