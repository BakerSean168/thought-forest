---
tags:
  - tech/ops/cloud
  - type/concept
  - status/growing
description: GitHub-Codespaces
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---



# GitHub Codespaces

GitHub Codespaces 是一个由 GitHub 提供的即时、云端开发环境。它允许你通过浏览器或本地 VS Code 直接访问一个已经配置好的、运行在云端虚拟机上的开发容器。其核心目标是让你摆脱繁琐的本地环境配置，实现“随时随地、任何设备”都能编码的理想状态，尤其适合处理像 `litemall` 这样需要同时运行多个服务的复杂项目。

## 基础概念

*   **云端开发环境 (Cloud IDE)**：你的所有代码、依赖、编译器、服务都运行在 GitHub 的云服务器上。你的本地电脑只负责显示界面和输入，从而彻底解放本地的 CPU 和内存资源。
*   **开发容器 (Dev Container)**：每个 Codespace 都运行在一个 Docker 容器中。这意味着环境是隔离的、可复现的。你可以通过项目根目录下的 `.devcontainer/devcontainer.json` 文件来精确自定义环境，例如指定所需的操作系统、工具版本（如 Java 11, Node.js 16）、自动安装 VS Code 插件等。
*   **端口转发 (Port Forwarding)**：这是 Codespaces 的一个核心亮点。当你在 Codespaces 的终端里启动一个服务（例如 Spring Boot 的 8080 端口），Codespaces 会自动检测到并将其“转发”到一个安全的公网 URL 上。这意味着你可以像访问普通网站一样，通过这个 URL 访问你正在开发中的应用，非常便于调试和分享。
*   **按需使用与自动休眠**：Codespaces 是按使用时长计费的（但有慷慨的免费额度）。为了节省资源，当它检测到你一段时间没有操作时，会自动进入休眠状态。你的所有修改都会被保存，下次可以快速唤醒。

## 使用指南

1.  **启动 Codespace**：
	*   进入你的 GitHub 仓库页面（例如 `AJ1E/litemall`）。
	*   点击绿色的 `<> Code` 按钮。
	*   切换到 "Codespaces" 标签页。
	*   点击 "Create codespace on main" 按钮。只需等待几分钟，一个为你准备好的云端 VS Code 编辑器就会在浏览器中打开。

2.  **熟悉界面**：
	*   展现在你面前的是一个功能完整的 VS Code 编辑器，包括文件浏览器、代码编辑区、集成终端、Git 管理等，使用体验与桌面版几乎一致。

3.  **使用终端**：
	*   通过顶部菜单 `Terminal > New Terminal` 或快捷键 ``Ctrl+` `` 打开终端。
	*   你可以在这里执行所有 Linux 命令，例如 `mvn clean package`, `npm install`, `java -jar xxx.jar` 等，就像在本地一样。

4.  **管理端口**：
	*   当你启动一个服务后，可以在 **PORTS** 标签页（通常在终端旁边）看到所有被自动转发的端口。
	*   你可以看到每个端口对应的“本地地址”（如 `localhost:8080`）和“转发后的公网地址”。你还可以手动更改端口的可见性（私有或公开）。

5.  **生命周期管理**：
	*   **关闭**：直接关闭浏览器标签页即可。环境会在设定的非活动时间（默认30分钟）后自动休眠。
	*   **手动停止**：在你的仓库 "Codespaces" 列表页面，可以手动停止一个正在运行的环境以节省免费额度。
	*   **删除**：如果一个环境不再需要，可以彻底删除它以释放存储空间。

## 实战经验 (以 `litemall` 项目为例)

下面是如何利用 Codespaces 来开发你的 `litemall` 毕业设计：

1.  **启动环境**：在你的 `AJ1E/litemall` 仓库中创建一个新的 Codespace。
2.  **准备数据库**：
	*   **方案A (最简单)**：Codespaces 的默认环境通常已包含 MySQL 客户端。你可以连接到一个**免费的云数据库**（例如 [PlanetScale](https://planetscale.com/), [Neon](https://neon.tech/) 的免费套餐）。这样数据就能持久化，不受 Codespace 删除的影响。
	*   **方案B (临时)**：在 Codespaces 终端中安装并启动一个 MySQL 服务，然后导入 `litemall` 的 SQL 文件。这种方式最快，但数据会在 Codespace 被删除后丢失，适合快速测试。

3.  **运行后端服务**：
	*   在 VS Code 终端中，修改 `litemall-all/src/main/resources/application-core.yml`，将数据库连接信息指向你的云数据库。
	*   运行启动命令。你可以直接在 `Application.java` 上右键 "Run Java"，或者通过 Maven 运行。
	*   启动成功后，**PORTS** 标签页会自动出现 `8080` 端口的转发记录。复制这个**公网 URL**，这就是你后端服务的线上地址。

4.  **运行管理后台**：
	*   新开一个终端，进入 `litemall-admin` 目录。
	*   运行 `npm install` 和 `npm run dev`。
	*   **PORTS** 标签页会再次出现 `9527` 端口的转发记录。点击它对应的公网 URL，你就可以在浏览器新标签页中访问你的管理后台了。

5.  **调试微信小程序**：
	*   这是整个流程中最关键的一步，也是云开发优势的体现。
	*   在你**本地电脑**上，打开微信开发者工具，导入 `litemall-wx` 项目。
	*   修改 `litemall-wx/config/api.js` 文件。
	*   将 `WxApiRoot` 的值，从 `http://127.0.0.1:8080/wx/` 修改为你在第 3 步中获得的**后端服务公网 URL**，并在末尾加上 `/wx/`。
	*   现在，你本地的微信开发者工具，请求的就是运行在云端 Codespaces 上的后端服务了！你本地电脑无需再承担任何后端计算压力。

## 高阶技巧

### 地区配置

可以在 codespace 设置页里有 默认区域 选项 可以配置地区
`curl ifconfig.me` 查询当前机器 ip 
然后查询[[resource-normal#IP查询]]

### 升级配置

```powershell
gh codespace list

gh api /user/codespaces/solid-space-robot-4jq6gwgg767x374g5/machines

gh codespace edit --machine standardLinux32gb

gh codespace edit --machine standardLinux32gb --codespace solid-space-robot-4jq6gwgg767x374g5

gh api /user/codespaces/solid-space-robot-4jq6gwgg767x374g5 | jq '.machine'

gh codespace stop --codespace solid-space-robot-4jq6gwgg767x374g5
```

### 使用 本地 vscode 的 github codespace 工作流

将本地的 VS Code 连接到远程的 GitHub Codespace 是官方支持的核心功能之一，也是许多开发者更偏爱的工作流。这让你既能享受到云端环境的强大性能和一致性，又能使用自己最熟悉、配置最完善的本地编辑器。

这背后的技术正是 VS Code Remote Development 扩展包的一部分。


安装 "GitHub Codespaces" 扩展

安装完成后，点击 VS Code 左下角绿色的 远程窗口(Remote Window) 按钮（通常显示为 >< 符号）。
在弹出的命令面板中，选择 "Connect to Codespace..."。
VS Code 会自动从你的 GitHub 账号获取你所有可用的 Codespaces 列表（包括正在运行和已停止的）。
从列表中选择你想要连接的那个 Codespace。
VS Code 会打开一个新窗口，并自动连接到云端的环境。连接成功后，左下角的绿色按钮会显示你的 Codespace 名称，例如 >< Codespaces: AJ1E/litemall。
开始工作

现在，你在这个新窗口中的所有操作——打开终端、编辑文件、运行调试——都是在云端的 Codespace 虚拟机上执行的。
你的本地 VS Code 实际上变成了一个功能强大的“客户端”，而所有的计算任务（如编译 Java 代码、运行 npm run dev）都由云端服务器承担。

为什么这是一个更好的工作方式？
- 极致的个性化体验：你可以使用你本地 VS Code 上已经配置好的所有主题、快捷键、UI 布局和客户端扩展，无需在浏览器环境中重新配置。
- 更原生的性能和感觉：对于编辑器本身的 UI 交互（滚动、输入、窗口管理），本地应用的响应速度通常会比浏览器版本感觉更流畅、更“跟手”。
- 无缝的本地集成：可以更方便地与你本地的其他工具链或工作流（如本地的数据库客户端、API 测试工具等）进行交互。
- 稳定的连接：即使你不小心关闭了 VS Code 窗口，云端的 Codespace 依然在运行。你可以随时重新连接，所有的终端会话和正在运行的服务都会保持原样。

总结来说，"本地 VS Code + 远程 Codespaces" 的组合，集成了两者的最大优点：既有云端开发环境的便捷和强大，又有本地编辑器最舒适的个性化体验。
## 信息参考

*   **官方文档**: [GitHub Codespaces Documentation](https://docs.github.com/en/codespaces) - 最权威、最全面的信息来源。
*   **免费额度说明**: 每个 GitHub Free 用户每月都享有免费的核心小时数（core-hours）和存储空间。具体额度可以在你的 GitHub 个人设置的 "Billing and plans > Plans and usage" 中查看。对于学生，如果申请了 [GitHub Student Developer Pack](https://education.github.com/pack)，可能会获得更高级别的免费额度。
*   **`.devcontainer` 自定义配置**: [Dev Containers Specification](https://containers.dev/) - 如果你想深入定制你的开发环境（例如，让 Codespace 在创建时就自动安装好所有依赖），可以学习 `devcontainer.json` 的配置方法。


