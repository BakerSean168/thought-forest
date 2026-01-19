---
tags:
  - tech/ai/ml
  - type/concept
  - status/seed
description: Transformer
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# Transformer

## 基础概念

论文：Vaswani et al. 2017 《Attention Is All You Need》

核心动机：RNN/Seq2Seq 难以并行 & 远距离依赖衰减；卷积需多层堆叠扩大感受野。自注意力一次性全局对齐，配合位置编码保留序列顺序。

高层结构：Encoder N 层 + Decoder N 层；Encoder 仅自注意力 + FFN；Decoder 包含 masked 自注意力 + cross-attention + FFN。

并行优势：序列 token 完全并行（除 Decoder 训练中 target shift），显著缩短训练时间。

## 核心组件

1. Multi-Head Self-Attention (MHSA)
2. Position-wise Feed Forward Network (FFN)
3. Add & LayerNorm（残差）
4. Positional Encoding（正余弦）
5. Mask（Padding Mask / Subsequent Mask）
6. Scaled Dot-Product Attention：softmax(QK^T/√d) V
7. Label Smoothing（论文使用 0.1）

注意力计算：
```python
import torch, math
def attention(q,k,v,mask=None):
  score = (q @ k.transpose(-2,-1)) / math.sqrt(q.size(-1))
  if mask is not None:
    score = score.masked_fill(mask==0, float('-inf'))
  attn = score.softmax(-1)
  return attn @ v, attn
```

位置编码：
PE(pos,2i) = sin(pos/10000^{2i/d_model})
PE(pos,2i+1)= cos(pos/10000^{2i/d_model})

FFN：两个线性层 + 激活（ReLU/GELU/Swish 现代变体）。

LayerNorm 位置：原论文使用 Post-LN（残差后），后续 Pre-LN 更稳定（梯度更易传播）。

Decoder mask：防止模型看到未来 token（保持自回归）。

参数复杂度：O(L^2 * d) 受限于注意力矩阵；长序列优化发展出 Sparse / Linear / Performer / FlashAttention / MQA / GQA 等变体。

## 训练要点

1. 学习率调度：Warmup + 反向 sqrt 衰减（论文 warmup=4000）
2. 正则：Label Smoothing、Dropout(MHSA & FFN & 残差)
3. 权重初始化：Xavier / kaiming；Embedding 与输出共享权重可节省参数
4. Mixed Precision：加速与节省显存；注意 LayerNorm 精度
5. 梯度裁剪：避免梯度爆炸（尤其初期）
6. 大模型稳定：Pre-LN、规范化策略（RMSNorm）、Scale 缩放初始化（DeepNet）
7. 位置表示替代：相对位置编码（T5 / Transformer-XL / RoPE / ALiBi）提升泛化与长上下文
8. 推理缓存：Decoder 自回归时缓存 K/V 减少重复计算
9. 长序列策略：Chunk + Sliding Window；FlashAttention 2 提升显存/吞吐
10. 模型裁剪/量化：4/8 bit（QLoRA）、蒸馏（DistilBERT）

Evaluation 指标：机器翻译 BLEU、语言建模 Perplexity、分类 accuracy/F1。

## 典型改进路线

Encoder-only：BERT / RoBERTa（MLM 预训练）
Decoder-only：GPT 系列（自回归生成）
Encoder-Decoder：T5 / BART（序列到序列）

关键演化：
- Efficient Attention（Linformer / Performer / Longformer / BigBird）
- 预训练目标多样（MLM、T5 span corruption、Prefix LM）
- Scaling Laws（Chinchilla 定律：数据/参数配比）
- Instruction Tuning / RLHF / DPO
- 多模态扩展（Vision Transformer, Flamingo, LLaVA）

未来方向：更高效稀疏注意力、上下文压缩、外部记忆检索（RAG）、低比特训练、能耗优化。

## 信息参考

- 原始论文: <https://arxiv.org/abs/1706.03762>
- Annotated Transformer: <http://nlp.seas.harvard.edu/2018/04/03/attention.html>
- FlashAttention: <https://arxiv.org/abs/2205.14135>
- Scaling Laws: <https://arxiv.org/abs/2001.08361>
- RoPE: <https://arxiv.org/abs/2104.09864>
- ALiBi: <https://arxiv.org/abs/2108.12409>

