---
tags: [status/growing, type/concept]
description: base_template
created: 2025-12-27T11:08:36
updated: 2025-12-27T11:09:12
---

# PCIe 

**PCIe (Peripheral Component Interconnect Express)** 是电脑里最快的数据传输总线。

- **干什么用：** 它是一个通用通道。你可以往上面插：
	- **显卡**（像你旧电脑里的 GT 730）。
	- **NVMe SSD**（那种像口香糖一样的小硬盘，速度比 SATA 快 10 倍以上）。
	- **高速网卡**（比如 2.5G 或 10G 网卡）。
- **什么是“通道 (Lanes)”：**
	- 你会看到 $x1, x4, x8, x16$ 这样的字样。这代表高速公路的**车道数**。
	- $x16$ 通常给显卡用，带宽最大；$x1$ 可能给无线网卡用。
- **什么是“代 (Generation)”：**
	- 目前主流是 PCIe 3.0 和 4.0。代数越高，每一条车道的速度就越快。