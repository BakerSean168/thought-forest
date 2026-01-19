---
tags:
  - tech/ai/ml
  - type/concept
  - status/seed
description: Transformers
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# Transformers

## 基础概念

组件：
1. Auto* 家族：AutoModel / AutoModelForSequenceClassification / AutoTokenizer / AutoProcessor
2. Tokenizer：子词（BPE、WordPiece、SentencePiece）
3. 模型权重：`from_pretrained`（HF Hub / 本地路径）
4. Pipeline：快速推理封装（task 级别）
5. Trainer：高层训练循环（日志/保存/评估/分布式）
6. Accelerate：统一多 GPU / TPU / DeepSpeed / FSDP
7. PEFT：参数高效微调（LoRA / Prefix / P-Tuning）
8. safetensors：安全 + 零拷贝加载格式

支持任务：分类、序列标注、问答、生成、翻译、摘要、对话、多模态（图像->文本、语音识别）、RAG（结合 `datasets` + `faiss`）。

## 使用指南

### 快速推理（pipeline）
```python
from transformers import pipeline
clf = pipeline('sentiment-analysis', model='distilbert-base-uncased-finetuned-sst-2-english')
print(clf('I love this framework'))
```

### 手动加载
```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification
tok = AutoTokenizer.from_pretrained('distilbert-base-uncased')
model = AutoModelForSequenceClassification.from_pretrained('distilbert-base-uncased')
inputs = tok('hello world', return_tensors='pt')
out = model(**inputs)
print(out.logits)
```

### 生成模型 (text-generation)
```python
from transformers import AutoModelForCausalLM, AutoTokenizer
model_name = 'gpt2'
tok = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)
ids = tok('Once upon a time', return_tensors='pt').input_ids
gen = model.generate(ids, max_length=50, do_sample=True, top_p=0.9, temperature=0.8)
print(tok.decode(gen[0], skip_special_tokens=True))
```

### Trainer 微调
```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer, Trainer, TrainingArguments
import datasets
ds = datasets.load_dataset('glue','sst2')
tok = AutoTokenizer.from_pretrained('distilbert-base-uncased')

def encode(batch):
  return tok(batch['sentence'], truncation=True, padding='max_length', max_length=128)
ds_enc = ds.map(encode, batched=True)
cols = ['input_ids','attention_mask','label']
ds_enc = ds_enc.remove_columns([c for c in ds_enc['train'].column_names if c not in cols])
model = AutoModelForSequenceClassification.from_pretrained('distilbert-base-uncased', num_labels=2)
args = TrainingArguments('out',
  per_device_train_batch_size=32,
  per_device_eval_batch_size=32,
  evaluation_strategy='epoch',
  logging_steps=50,
  save_strategy='epoch',
  learning_rate=5e-5,
  num_train_epochs=3,
  load_best_model_at_end=True
)
trainer = Trainer(model, args, train_dataset=ds_enc['train'], eval_dataset=ds_enc['validation'])
trainer.train()
```

### Accelerate + LoRA (PEFT)
```python
from peft import LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM, AutoTokenizer
model = AutoModelForCausalLM.from_pretrained('facebook/opt-1.3b', device_map='auto')
tok = AutoTokenizer.from_pretrained('facebook/opt-1.3b')
lora_cfg = LoraConfig(r=8, lora_alpha=16, target_modules=['q_proj','v_proj'], lora_dropout=0.05, bias='none')
model = get_peft_model(model, lora_cfg)
model.print_trainable_parameters()
```

### RAG 概念
1. 文档切分 embedding
2. 向量检索 top-k
3. 拼接上下文 -> 发送给生成模型
可结合 `langchain` / `faiss` / `datasets`。

### 量化加载（bitsandbytes）
```python
model = AutoModelForCausalLM.from_pretrained('meta-llama/Llama-2-7b-hf', load_in_4bit=True, device_map='auto')
```

### safetensors
避免 pickle 风险；加载更快：`from_pretrained(..., use_safetensors=True)`。

### tokenizer 并行
`TOKENIZERS_PARALLELISM=false` 可抑制多线程警告。

## 实战经验

优化方向：
1. 数据管线：提前 tokenize 并序列化（arrow / parquet）
2. 多 GPU：Accelerate / DeepSpeed Stage 2/3 / FSDP
3. 参数高效：LoRA / QLoRA / Prefix / AdapterFusion
4. 长上下文：RoPE 插值 / Position Interpolation / 支持 ALiBi 模型
5. 推理优化：`attention_mask` 精简、FlashAttention、KV Cache、量化 (4/8-bit)、裁剪未用层
6. 分片加载：`device_map='auto'` + `max_memory` 限制
7. Prompt 工程：系统提示 + Few-shot + 指令模板
8. RAG 结合知识库减少幻觉
9. 评估：task metric + 生成质量(QA 正确率, 事实性) + 速度/显存
10. 模型版权与许可审查（LLaMA / Mistral / Qwen 条款）

常见问题：
- CUDA OOM：减小 batch / gradient accumulation / 量化 + LoRA
- Token 不匹配：tokenizer 与 model 需对应；增量新 token 需 resize embeddings
- 生成退化：temperature=1, top_p=1 导致重复；可用 repetition_penalty / top-k / nucleus
- 中文分词差：选择 sentencepiece 或 bpe 中文模型；确保正常 normalization
- 长输入截断：关注 `max_position_embeddings`

## 经验总结

高层抽象快速迭代（pipeline/Trainer），向下可手写训练循环获得灵活性。PEFT + 量化成为主流微调组合。RAG 与工具/向量库结合是企业知识应用关键路径。

选择策略：
- 小任务 + 快速验证：distil / base 级模型
- 中文/多语：mBERT, XLM-R, Qwen, Mistral (多语言 variant)
- 生成精度：llama2 / mistral / qwen / phi-2 (轻量)
- 超大规模：FSDP + ZeRO + LoRA 混合

## 信息参考

- 官方文档: <https://huggingface.co/docs/transformers>
- 模型库: <https://huggingface.co/models>
- Accelerate: <https://huggingface.co/docs/accelerate>
- PEFT: <https://huggingface.co/docs/peft>
- Datasets: <https://huggingface.co/docs/datasets>
- Tokenizers: <https://huggingface.co/docs/tokenizers>

