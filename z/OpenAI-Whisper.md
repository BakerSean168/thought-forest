---
tags:
  - tech/ai/ml
  - type/concept
  - status/growing
description: OpenAI-Whisper
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# OpenAI Whisper

## 基础概念

特点：
1. 端到端 Transformer 编码器-解码器（Audio -> Text）
2. 训练数据：多语言 + 噪声 + 口音（鲁棒性强）
3. 模式：转录(transcribe) / 翻译(translate) / 语言识别 / 时间戳生成
4. 模型尺寸：tiny / base / small / medium / large / large-v2 / large-v3（越大越准）
5. 输入：16kHz 单声道，切分为 30 秒窗口（不足填充，超长截断或分片）
6. 特殊 tokens：任务控制 <|translate|> / <|transcribe|>，<|notimestamps|>
7. 时间戳：通过解码端特殊时间 token 预测（可逐词/子词对齐）

适用场景：会议记录、视频字幕、客服质检、播客整理、多语言翻译、语音检索预处理。

## 使用指南

### 安装
```bash
pip install -U openai-whisper  # 官方 Python 实现，依赖 ffmpeg
pip install faster-whisper     # CTranslate2 后端加速
```

### 基本转录（官方）
```python
import whisper
model = whisper.load_model('small')
res = model.transcribe('audio.mp3', task='transcribe')
print(res['text'])
```

### 翻译到英文
```python
res = model.transcribe('chinese.wav', task='translate')
print(res['text'])
```

### 加速：faster-whisper
```python
from faster_whisper import WhisperModel
model = WhisperModel('small', device='cuda', compute_type='float16')
segments, info = model.transcribe('audio.mp3', beam_size=5, vad_filter=True)
for seg in segments:
  print(f"[{seg.start:.2f} -> {seg.end:.2f}] {seg.text}")
```

### 语言检测
```python
import whisper
audio = whisper.load_audio('sample.wav')
mel = whisper.log_mel_spectrogram(audio)
model = whisper.load_model('base')
_, probs = model.detect_language(mel)
print(probs)
```

### 常用参数
- temperature：采样多样性（0 ~ 1）
- beam_size / best_of：束搜索与候选
- condition_on_previous_text：利用前一段上下文（长录音连续性）
- fp16=True：GPU 半精度（CPU 默认禁用）

### 长音频处理流程（推荐）
1. 重采样 16k + 单声道
2. VAD (Silero/WebRTC) 切分静音
3. 对段落添加少量重叠（1~2s）减缓边界截断
4. 并行处理分片（控制 GPU 占用）
5. 合并 & 时间戳对齐 & 去重叠
6. 后处理（标点、格式化、术语替换）

### Web 服务（SSE / WebSocket）设计要点
- 上传：流式 / 分段断点续传
- 队列：限流 + 按长度/优先级排序
- 推理：批处理相似长度片段；GPU 多流
- 输出：增量字幕 JSON {start,end,text}
- 编辑：客户端可回写校正 -> 保存版本

## 实战经验

1. 模型取舍：`small` 开发验证；`medium` 兼顾速度精度；`large-v3` 生产精度
2. 预处理：统一 loudness，去除强噪声（谱减 / denoise）提升识别
3. 中文场景：指定 `language='zh'` 减少检测错误
4. 翻译增强：产出英文后再用大语言模型润色 / 术语替换
5. 时间戳：逐词对齐需开启 `word_timestamps`（faster-whisper），占用 ↑
6. 并发：多 GPU -> 文件分片分配；单 GPU -> 任务队列 + 限速
7. 缓存：对重复音频哈希缓存结果（MD5 of PCM）
8. 敏感词：后处理词典扫描 / 模糊匹配（Aho-Corasick）
9. 审核：保存原音轨 + JSON 结果（便于复审）
10. 成本：短音频聚合批推理，降低 kernel launch overhead

常见问题：
- 输出空白：音频无语音 / 格式不兼容（确认 ffmpeg 转换）
- 语言检测错：手动指定 language
- 内存爆：过多并发进程；限制同时 segment 数
- 大幅延迟：无 VAD 导致大量静音处理；需预切分
- 翻译混杂原文：未设置正确 task

## 经验总结

Whisper 主打鲁棒通用性，非领域极致精度；可借助领域词典/二阶段语言模型校正。加速路线：faster-whisper -> ONNX -> TensorRT；准确率提升：清洗音频 + 语种指定 + 合理 beam；吞吐优化：分段并行 + 缓存 + 量化(float16/int8)。

## 信息参考

- 官方仓库: <https://github.com/openai/whisper>
- faster-whisper: <https://github.com/guillaumekln/faster-whisper>
- Silero VAD: <https://github.com/snakers4/silero-vad>
- WebRTC VAD: <https://github.com/wiseman/py-webrtcvad>
- 字幕格式：SRT / WebVTT / ASS

