---
tags:
  - status/growing
  - type/concept
  - tech/hardware/header
description: base_template
created: 2026-01-18T10:25:53
updated: 2026-01-18T10:30:13
---

# F_AUDIO 

![[F_AUDIO.png]]

这是一个标准的 **前置音频接口 (Front Panel Audio Header)**。

在你的 N100 主板上，这个标记为 **F_AUDIO1** 的接口用于连接机箱前面板的 **耳机插孔** 和 **麦克风插孔**。

以下是关于这个 Header 的详细介绍：

### 1. 核心身份

- **名称**: F_AUDIO1 (Front Audio).
- **类型**: 标准的 **9 针 (Standard 9-Pin)** 音频插座。
- **物理结构**: 虽然看起来是 2x5 的 10 针位置，但**第 8 针是空的 (Key)**。这是一种防呆设计，防止你插反。

### 2. 标准与定义

这类接口通常遵循 Intel 制定的 **HD Audio (High Definition Audio)** 标准（旧的主板可能是 AC'97，但 N100 这种新平台肯定是 HD Audio）。

标准的 HD Audio 针脚定义如下（供参考，通常直接插机箱线即可）：

- **Pin 1**: Mic Left (麦克风左声道)
- **Pin 2**: Ground (地线)
- **Pin 3**: Mic Right (麦克风右声道)
- **Pin 4**: Presence# (插入检测，告诉主板前面板插了东西)
- **Pin 5**: Headphone Right (耳机右声道)
- **Pin 6**: Sense1_Ret (麦克风插入感应返回)
- **Pin 7**: Sense_Send (感应发送)
- **Pin 8**: **空 (无针脚)**
- **Pin 9**: Headphone Left (耳机左声道)
- **Pin 10**: Sense2_Ret (耳机插入感应返回)

### 3. 使用建议

- **如果你做桌面电脑/HTPC**: 这是一个非常有用的接口。将机箱前面板那根写着 `HD AUDIO` 的插头插在这里，你就可以直接在机箱前面插耳机听歌或插麦克风开会。
- **如果你做软路由/服务器 (Home Lab)**: **这个接口完全没用**。你可以把它空着，因为服务器通常不需要在前面板接耳机，而且很多工控/NAS 机箱甚至没有提供音频线。

### 4. 如何连接

- 找到你机箱上那根写着 **AUDIO** 或 **HD AUDIO** 的线。
- 观察插头，上面也会有一个堵死的孔。
- 将插头堵死的孔对准主板上 **缺针的位置 (Pin 8)**，直接插进去即可。由于有防呆设计，插不进去就是方向错了，不要硬插。

**位置提示**: 从主板全景图来看，这个接口位于主板的**左下角**，紧挨着主板边缘和螺丝孔，这通常也是声卡芯片所在的区域。