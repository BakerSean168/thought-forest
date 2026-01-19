---
tags: [status/growing, type/concept]
description: base_template
created: 2026-01-08T10:46:01
updated: 2026-01-08T10:46:22
---

# 密码分组链接模式 (CBC)


## 1. 定义

**密码分组链接模式 (Cipher Block Chaining, CBC)** 是为了解决 ECB 模式泄露明文特征问题而设计的。
[cite_start]它的核心思想是：当前的密文块不仅取决于当前的明文块，还取决于前一个密文块 [cite: 831, 844]。

## 2. 工作原理

引入了一个**初始向量 (Initialization Vector, IV)** 来处理第一个分组。

* **加密**：
	1.  先将明文分组 $P_i$ 与前一个密文分组 $C_{i-1}$ 进行**异或 ($\oplus$)** 运算。
	2.  再对异或结果进行加密。

	$$

C_i = E_K(P_i \oplus C_{i-1})

$$
    [cite_start]*(注：对于第一个块，$C_0$ 用 $IV$ 代替)* [cite: 844]。

* **解密**：
    $$
P_i = D_K(C_i) \oplus C_{i-1}
$$

## 3. 优缺点分析

[cite_start]根据 PPT [cite: 848-853] 总结：

### 优点

1.  [cite_start]**安全性高**：引入随机的 IV 和链接机制，相同的明文在不同位置或不同 IV 下会生成不同的密文，隐藏了明文模式 [cite: 848]。
2.  [cite_start]**业界标准**：是大多数安全系统（如 SSL、IPSec）的标准工作模式 [cite: 849]。

### 缺点

1.  [cite_start]**无法并行加密**：加密必须顺序进行，因为第 $i$ 块依赖于第 $i-1$ 块的结果 [cite: 850]。
2.  [cite_start]**需要 IV**：发送方和接收方需要共同协商一个 IV [cite: 851]。
3.  **存在误差传递 (Error Propagation)**：
	* 如果一个密文块 $C_i$ 在传输中损坏，会导致：
		1.  当前块 $P_i$ 解密完全错误。
		2.  [cite_start]下一块 $P_{i+1}$ 的对应位解密错误（因为 $P_{i+1}$ 依赖于 $C_i$ 进行异或） [cite: 853]。

---

**相关链接**：
- [[电子密码本模式ECB]]
- [[密码反馈模式CFB]]
- [[异或运算]]