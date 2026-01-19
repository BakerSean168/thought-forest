---
tags:
  - status/growing
  - type/concept
  - tech/dev
description: base_template
created: 2025-12-30T18:26:08
updated: 2025-12-30T18:26:45
---

# Byte-Order-Mark 

## Overview

**BOM (Byte Order Mark)** 是 UTF-8/UTF-16 文件开头的特殊字节序列：
- UTF-8 BOM: `EF BB BF` (3个字节)
- 在文本编辑器中**不可见**，但会被解析器读取
- 用于标识文件编码，但对 UTF-8 来说是**可选且不推荐**的

## 📖 BOM 问题详解

### 报错原因

```
> 1 | ﻿{
    | ^
```

这里的 `﻿` 就是 BOM 字符（U+FEFF），虽然肉眼看不见，但：

1. **Nx 的 JSON 解析器**期望文件第一个字符是 `{`
2. 实际上第一个字符是 BOM (`U+FEFF`)
3. JSON 标准**不允许**在开头有 BOM
4. 解析器在位置 `1:1` 遇到非法字符，抛出 `InvalidSymbol` 错误

### 为什么会出现 BOM？

可能的原因：

1. **PowerShell 的 `Set-Content`**：

   ```powershell
   # ❌ 默认会添加 BOM
   Set-Content file.json -Encoding UTF8
   
   # ✅ 需要明确指定无 BOM
   [System.IO.File]::WriteAllText("file.json", $content, [System.Text.UTF8Encoding]::new($false))
   ```

2. **某些文本编辑器**：
   - Windows 记事本旧版本
   - 某些配置不当的 IDE

3. **脚本/工具自动生成**：
   - 之前用 PowerShell 的 `-replace` 命令修改文件时，可能自动添加了 BOM

### 技术细节

```
文件实际内容（十六进制）：
EF BB BF 7B 0A 20 20 22 6E 61 6D 65 ...
   BOM    {  \n    "  n  a  m  e  ...

解析器期望：
7B 0A 20 20 22 6E 61 6D 65 ...
{  \n    "  n  a  m  e  ...
```

### 解决方案

我们使用的命令：

```powershell
[System.Text.UTF8Encoding]::new($false)
#                                  ↑
#                          false = 不要 BOM
```

这确保文件保存为**纯 UTF-8 无 BOM**，符合 JSON 标准和 Nx 的要求。

### 预防措施

1. **VS Code 设置**：确保 `"files.encoding": "utf8"` (默认无 BOM)
2. **避免使用** PowerShell 的 `Set-Content -Encoding UTF8`
3. **Git 配置**：设置 `.gitattributes` 明确文件编码
4. **EditorConfig**：在 `.editorconfig` 中指定 `charset = utf-8`

这就是为什么一个看不见的字符会导致整个项目无法解析的原因！