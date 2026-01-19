---
tags:
  - tech/ai/ml
  - type/howto
  - status/growing
description: 七牛云ai调用
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[AI MOC]] | [[学科知识 MOC]]

---


# 七牛云ai调用 

## 基础概念

使用七牛云提供的接口来为项目提供 ai 能力

## 使用指南

### 配置

1. 创建个人 API Key
2. 从帮助文档获取接入点
	1. - **主接入点**：`https://openai.qiniu.com/v1`
	2. **备接入点**：`https://api.qnaigc.com/v1`

### 基础接口

### 支持接口列表

|接口名|说明|
|---|---|
|/v1/chat/completions|对话型推理接口(兼容OpenAI格式)，支持图片文字识别、文件识别  <br>图片格式：支持 JPG、JPEG、PNG、BMP、PDF 等常见格式，建议使用 JPG 格式  <br>联网搜索：部分模型支持，通过在模型名后添加"?search"启用|
|/v1/models|列举所有可用模型ID及参数|
|/v1/messages|兼容Anthropic API格式，另参考[Claude Code配置](https://developer.qiniu.com/aitokenapi/13085/claude-code-configuration-instructions?portal_modal=1)|

## 实战经验

### 通过 apifox 来测试

## 经验总结

## 信息参考
