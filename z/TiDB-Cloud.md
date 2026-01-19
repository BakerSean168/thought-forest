---
tags:
  - tech/ops/database
  - type/concept
  - status/seed
description: TiDB Cloud分布式数据库云服务
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[数据库 MOC]] | [[后端开发 MOC]]

---

### TiDB Cloud

TiDB 是一个开源的、与 MySQL 语法和协议高度兼容的分布式数据库。TiDB Cloud 是其官方的云托管服务，同样提供了一个非常适合开发者的免费套餐。

- **官网**: [https://tidb.cloud/](https://tidb.cloud/)
- **兼容性**: **高度兼容 MySQL**。你的 `litemall` 项目几乎不需要修改任何代码，只需更换 JDBC 连接字符串即可。
- **免费套餐 (Developer Tier)**:
    - 提供一个 Serverless 数据库集群。
    - 包含一定的免费存储、计算和请求单元（称为 RU），对于毕业设计或个人项目来说完全足够。
    - 支持数据库分支功能（beta），体验上与 PlanetScale 类似。
- **为什么推荐它**:
    1. **无缝迁移**：对 `litemall` 这种 MySQL 项目来说，这是最平滑的过渡方案。
    2. **现代架构**：和 PlanetScale 一样，它也是基于现代云原生架构，让你能体验到前沿技术。
    3. **上手简单**：注册后，只需几分钟就可以创建一个集群并获取连接字符串。

**实战步骤**:

1. 用 GitHub 账号注册 TiDB Cloud。
2. 创建一个新的 Serverless 集群。
3. 在集群的 "Connect" 页面获取连接信息。
4. 将你的 `application-core.yml` 文件中的 `spring.datasource` 配置更新为 TiDB Cloud 提供的信息。
5. 使用任何 MySQL 客户端（如 Navicat, DBeaver）连接到 TiDB Cloud，并导入 `litemall` 的三个 SQL 文件。

## 配置

这是一个非常好的问题，问到了云开发流程中的核心关键点！

**简短的回答是：所有配置都应该按照 GitHub Codespace 的情况来选择和设置。**

下面是详细的解释，告诉你为什么以及具体该怎么做：

### 核心原则

数据库的连接配置，永远是给**发起连接的那个应用程序**准备的。

*   在你的场景中，发起数据库连接的程序是 `litemall` 的后端服务（那个 Java Jar 包）。
*   你计划在 **GitHub Codespace** 里面运行这个后端服务。
*   所以，所有的配置（IP地址、证书路径等）都必须是 **Codespace 环境里的配置**，而不是你本地 Windows 电脑的。

---

### 针对你 TiDB 配置的详细解读和操作步骤

你的 TiDB 连接信息非常清晰，我们来逐一分析并给出在 Codespace 中的正确操作方法：

#### 1. 操作系统选择 (Operating System)

*   **你看到的是**：`Windows (Detected)`
*   **原因**：这是因为你正在用你本地的 Windows 电脑**浏览** TiDB 的网站，所以它检测到了你的浏览器环境是 Windows。
*   **正确做法**：**忽略这个提示**。你的 Codespace 是一个 **Linux** 环境。所有操作，比如下载证书、文件路径等，都应该遵循 Linux 的方式。

`uname -a`

#### 2. IP 地址白名单 (IP Allowlist)

*   **你看到的是**：`Your current IP address (213.130.142.130) is allowed to connect.`
*   **原因**：TiDB 自动将你当前浏览网页所用的公网 IP 加入了白名单。
*   **【关键操作】**：你的 Codespace 作为一个独立的云端虚拟机，拥有**它自己的、完全不同的公网 IP**。为了让 Codespace 能够成功连接 TiDB，你**必须**把 Codespace 的 IP 地址也加入到白名单中。
    *   **第一步：找到 Codespace 的公网 IP**
        *   在 Codespace 的终端里，执行以下命令：
            ```bash
            curl ifconfig.me
            ```
        *   这会返回一串 IP 地址，例如 `20.205.243.166`。这就是你 Codespace 的公网 IP。
    *   **第二步：在 TiDB Cloud 中添加 IP**
        *   回到 TiDB Cloud 的网站，在你的集群设置里找到 "Network Access" 或 "网络访问"。
        *   点击 "Add IP Address"，将你刚刚获取到的 Codespace IP 添加进去。
    *   **【更简单的推荐方案】**：为了方便开发（因为 Codespace 重启后 IP 可能会变），你可以直接添加 `0.0.0.0/0` 到白名单。这代表**允许任何 IP 地址连接**，对于毕业设计阶段来说非常方便，省去了频繁修改 IP 的麻烦。

#### 3. CA 证书路径 (CA_PATH)

*   **你看到的是**：`<CA_PATH>`
*   **原因**：为了建立安全的 SSL 连接，JDBC 驱动需要读取这个 CA 证书文件。
*   **【关键操作】**：
    1.  **下载证书**：在 TiDB 的连接页面，点击 "Download the CA cert" 按钮，将证书文件（通常名为 `isrgrootx1.pem`）下载到你**本地的 Windows 电脑**上。
    2.  **上传到 Codespace**：在 Codespace 的 VS Code 界面中，从左侧的文件浏览器，直接将你下载的 `isrgrootx1.pem` 文件**拖拽**到 `litemall` 项目的根目录（或其他你喜欢的位置）。
    3.  **获取 Codespace 中的路径**：假设你把它放在了 `litemall` 项目的根目录，那么它在 Codespace 中的绝对路径就是 `/workspaces/litemall/isrgrootx1.pem`。
    4.  **配置 JDBC URL**：在 `application-core.yml` 文件中，你需要使用这个**在 Codespace 中的路径**来配置 JDBC URL。

---

### 最终的 `application-core.yml` 配置示例

综合以上所有步骤，你在 Codespace 中的 `litemall` 项目的 `application-core.yml` 文件看起来应该是这样的：

```yaml
spring:
  datasource:
    druid:
      # 1. 使用 TiDB 提供的 URL、用户名和密码
      url: jdbc:mysql://gateway01.eu-central-1.prod.aws.tidbcloud.com:4000/test?sslMode=VERIFY_IDENTITY&trustCertificateKeyStoreUrl=file:/workspaces/litemall/isrgrootx1.pem&trustCertificateKeyStoreType=JKS
      username: 3gw7gessje9T2Cv.root
      password: <你生成的密码>
      # 2. 这里的 url 参数 `trustCertificateKeyStoreUrl` 使用了证书在 Codespace 中的绝对路径
      driver-class-name: com.mysql.cj.jdbc.Driver
```

**注意**：上面 `url` 中的 `trustCertificateKeyStoreUrl=file:/workspaces/litemall/isrgrootx1.pem` 是一个示例，你需要根据你实际使用的 JDBC 驱动和版本来调整 SSL 相关的参数。常见的参数有：
*   `serverSslCert`
*   `trustCertificateKeyStoreUrl`
*   `trustStore`

请参考 TiDB 官方提供的 JDBC 连接示例和你项目中的 MySQL Connector/J 版本来确定最准确的参数写法。

**总结：一切以 Codespace 为准，因为它才是真正运行你后端代码的“服务器”。**
