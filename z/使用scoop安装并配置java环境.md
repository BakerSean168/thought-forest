---
tags: [type/howto, status/evergreen]
description: 如何xxx
created: 2025-12-24T19:02:25
updated: 2025-12-24T19:29:28
---

# 使用 scoop 安装并配置 java 环境 

## 将 java 桶添加

```powershell
# 1. 添加 Java 专用仓库
scoop bucket add java

# 2. 更新仓库信息
scoop update
```

## 搜索

搜索 openjdk 或者 temurin

| **特性**   | **openjdk21 (通常指 Oracle OpenJDK)**      | **temurin21-jdk (Eclipse Temurin)** |
| -------- | --------------------------------------- | ----------------------------------- |
| **发行方**  | Oracle 官方                               | Eclipse 基金会 (Adoptium 项目)           |
| **更新周期** | **仅 6 个月**。即便标为 LTS，Oracle 也会在半年后停止免费更新 | **长期支持 (LTS)**。提供数年的免费安全更新和维护       |
| **合规性**  | Java SE 参考实现                            | 通过了 TCK 测试，确保 100% 兼容 Java 规范       |
| **推荐场景** | 仅用于短期测试最新功能                             | **生产环境、长期开发** (开发者首选)               |

**建议选择 `temurin21-jdk`。**

- **稳定性：** Temurin（原名 AdoptOpenJDK）由 IBM、微软、红帽等大厂共同维护，是目前社区公认的最稳定、最主流的免费 JDK 分发版。
- **统一管理：** 在 Scoop 中安装后，它的路径会固定在 `D:\scoop\apps\temurin21-jdk`，非常方便配置环境变量。

直接用 **Scoop** 安装。可以同时安装 `openjdk11` 和 `openjdk17`，Scoop 会处理好 `JAVA_HOME`。如果需要频繁切换，可以使用 `scoop reset` 命令。

## 测试

无论你装的是 `openjdk` 还是 `temurin`，检测命令是一样的：

- **查看 Java 运行时：** `java -version`。
- **查看 Java 编译器：** `javac -version`。
- **PowerShell 特有检测：** `Get-Command java | Select-Object Version`（这能显示更详细的可执行文件信息）。
