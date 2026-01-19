---
tags:
  - tech/lang/typescript
  - type/concept
  - status/growing
description: playwright
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---

好的，我很乐意为您补充这份关于 **Playwright** 的全面文档。Playwright 是一个强大的工具，尤其在 Web 自动化和测试领域非常流行。

以下是根据您提供的提纲补充的详细内容：

# Playwright 🚀

## 1\. 概览（Overview）

  * **这是一个什么东西？**

      * Playwright 是一个 Node.js 库，用于**端到端 (End-to-End, E2E) 测试**、**Web 自动化**和**网页抓取（Scraping）**。
      * 它提供了一个单一 API 来控制 **Chromium** (Chrome/Edge)、**Firefox** 和 **WebKit** (Safari) 三大主流浏览器，且支持“无头”（Headless）和“有头”（Headed）模式。
      * 支持多种语言绑定，包括 **TypeScript/JavaScript** (原生)、**Python**、**Java** 和 **.NET**。

  * **它解决什么问题？适用场景是什么？**

      * **解决问题：** 解决了传统 E2E 测试工具（如 Selenium）在跨浏览器、现代 Web 功能（如 Shadow DOM、WebSockets、Service Workers）支持以及执行速度和稳定性方面的痛点。
      * **适用场景：**
        1.  **高可靠性的 E2E 测试：** 确保 Web 应用在所有主流浏览器上的功能正确性。
        2.  **Web 自动化流程：** 自动执行重复性的浏览器任务（例如表单填写、登录流程）。
        3.  **性能和兼容性测试：** 在不同浏览器引擎上测试应用的渲染和运行表现。
        4.  **网页爬虫/抓取：** 抓取需要 JavaScript 渲染的复杂网页内容。

  * **一句话总结**

    > **Playwright 是一个为现代 Web 量身定制的，能以快速、可靠、跨浏览器方式进行自动化和端到端测试的框架。**

-----

## 2\. 关键概念（Core Concepts）

  * 概念 A：**Browser / 浏览器实例**
      * 说明：代表一个浏览器进程的实例（如 Chromium 或 Firefox）。通过 `playwright.launch()` 启动。这是最高级别的容器。
  * 概念 B：**Context / 浏览器上下文**
      * 说明：模拟一个隔离的浏览器会话，类似于 Incognito（隐身）或 Private 模式。每个 Context 都是独立的，拥有自己的缓存、Cookies 和本地存储，可防止测试间相互污染。
  * 概念 C：**Page / 页面**
      * 说明：代表浏览器 Context 中的一个标签页 (`<Tab>`)。这是您与 Web 页面交互的主要 API。所有操作（点击、输入、导航）都在 Page 对象上进行。
  * **专有名词 / 缩写解释**
      * **Headless：** “无头”模式。浏览器在后台运行，没有可见的 GUI 界面，常用于 CI/CD 和服务器环境以提高性能。
      * **Codegen：** Playwright 的代码生成工具，可以录制用户在浏览器中的操作，并自动生成测试代码。
      * **Test Runner：** Playwright 自带的测试运行器 (`@playwright/test`)，专为 E2E 优化，提供并行执行、重试、测试隔离等功能。
      * **Locator：** Playwright 推荐的元素查找方式，它代表了一种查找元素的策略，而不是一个具体的元素，具有**自动等待**和**重试**的能力。

-----

## 3\. 安装与环境准备（Installation / Setup）

  * 系统要求
      * **操作系统：** Windows、macOS 或 Linux。
      * **Node.js：** 推荐 LTS 版本（例如 18 或更高版本）。
  * 安装步骤（命令行/GUI）
      * **安装 Playwright Test (Node.js/TypeScript/JavaScript):**
        ```bash
        npm init playwright@latest
        # 或 yarn create playwright
        ```
        该命令会引导您设置项目、选择 TypeScript/JavaScript，并自动安装依赖。
      * **安装浏览器驱动：**
        安装 Playwright 时会自动下载所需的浏览器二进制文件，但如果需要手动安装：
        ```bash
        npx playwright install
        # 或 npx playwright install chromium # 只安装特定浏览器
        ```
  * 环境变量 / 配置说明
      * **`PLAYWRIGHT_TEST_BASE_URL`:** 设置所有测试的基准 URL，例如 `http://localhost:3000`。
      * **`playwright.config.ts`：** 主配置文件。所有测试运行器的配置都集中在这里，包括项目设置、超时时间、报告生成等。

-----

## 4\. 快速开始（Quick Start）

  * 最小可运行示例 (TypeScript)
      * 文件：`tests/example.spec.ts`
    <!-- end list -->
    ```typescript
    import { test, expect } from '@playwright/test';

    test('has title', async ({ page }) => {
      // 1. 导航到指定的 URL
      await page.goto('https://playwright.dev/');

      // 2. 断言页面标题包含 'Playwright'
      await expect(page).toHaveTitle(/Playwright/);

      // 3. 找到 'Get started' 链接并点击
      await page.getByRole('link', { name: 'Get started' }).click();

      // 4. 断言 URL 已经变化
      await expect(page).toHaveURL(/.*intro/);
    });
    ```
  * 基本使用流程
    1.  **导入：** 从 `@playwright/test` 导入 `test` 和 `expect`。
    2.  **定义测试：** 使用 `test('测试名称', async ({ page }) => { ... });`。
    3.  **导航：** 使用 `await page.goto(url)`。
    4.  **查找元素：** 使用 **Locators** (如 `page.getByRole(...)` 或 `page.locator(...)`)。
    5.  **交互：** 使用 `.click()`, `.fill()`, `.selectOption()` 等方法。
    6.  **断言：** 使用 `await expect(locator).toBeVisible()` 或 `await expect(page).toHaveURL()`。
  * 示例代码 / 指令
      * **运行所有测试:**
        ```bash
        npx playwright test
        ```
      * **运行特定文件:**
        ```bash
        npx playwright test tests/example.spec.ts
        ```
      * **打开报告:**
        ```bash
        npx playwright show-report
        ```

-----

## 5\. 进阶使用（Advanced Usage）

  * 配置项说明
      * **`projects`：** 用于定义不同的测试配置集，例如：
        ```typescript
        projects: [
          { name: 'chromium', use: { browserName: 'chromium' } },
          { name: 'firefox', use: { browserName: 'firefox' } },
          { name: 'webkit', use: { browserName: 'webkit' } },
        ]
        ```
        运行时加上 `--project chromium` 即可执行特定项目。
      * **`retries`：** 设置测试失败后的重试次数，有助于处理偶发性的不稳定性。
      * **`timeout`：** 设置测试步骤或整个测试的最大执行时间。
  * 可扩展点 / 插件机制
      * **Test Fixtures (夹具)：** Playwright 的核心扩展机制。您可以自定义夹具来提供预处理的 Page 对象、认证状态、数据库连接或其他测试所需资源，实现**测试隔离和复用**。
      * **Hooks：** 使用 `test.beforeEach`, `test.afterAll` 等钩子函数来执行设置和清理工作。
  * 常见模式（patterns）
      * **Page Object Model (POM)：** 将页面元素和交互逻辑封装成独立的类，提高测试代码的可维护性和可读性。
      * **Global Setup/Teardown：** 在所有测试开始前执行一次设置（例如启动本地服务器）和在所有测试结束后执行一次清理。

-----

## 6\. 目录结构（Project Structure）

```
playwright-project/
 ├── node_modules/         # Node.js 依赖
 ├── tests/                # 存放所有的测试文件 (*.spec.ts)
 ├── pages/                # (可选) 存放 Page Object Model (POM) 文件
 ├── playwright.config.ts  # Playwright 配置主文件
 ├── package.json          # 项目依赖和脚本
 └── playwright-report/    # 自动生成的 HTML 报告目录
```

  * 每个目录的作用说明
      * **`tests/`：** **必须**。所有测试脚本 (`.spec.ts`, `.test.ts`, `.spec.js` 等) 都放在这里。
      * **`pages/`：** **可选/推荐**。用于存储 Page Object Model (POM) 类文件，将页面的选择器和操作抽象出来。
      * **`playwright.config.ts`：** **必须**。包含运行测试所需的一切配置（浏览器、超时、并行设置等）。
      * **`playwright-report/`：** 存放测试运行后的 HTML 报告，可查看每次测试的详细步骤、截图和视频。

-----

## 7\. 常见问题（FAQ）

  * **Q1: Playwright 与 Cypress 或 Selenium 有什么区别？**
      * **A:** Playwright 是一个**真正的跨浏览器**工具 (Chromium/Firefox/WebKit)，而 Cypress 只支持基于 Chromium 的浏览器。Playwright 在**多 Tab 页**、**多域**和**原生移动仿真**方面支持更好。Selenium 需要额外的驱动程序和维护，Playwright 则自带所有浏览器驱动，安装更简单。
  * **Q2: 如何处理 iframe 和 Shadow DOM 里面的元素？**
      * **A:** Playwright 对这些现代 Web 特性提供了**原生支持**。
          * **iframe:** 使用 `page.frameLocator('iframe selector').locator('element selector')`。
          * **Shadow DOM:** Playwright 的标准 `locator` 方法可以直接穿透 Shadow DOM 边界进行查找，无需特殊命令。

-----

## 8\. 排错指南（Debug / Troubleshooting）

  * 常见报错 & 解决方法
      * **Timeout error (超时错误):**
          * **原因:** 元素未在默认的 30 秒内出现或变得可操作。
          * **解决方法:**
            1.  确保使用了**正确的 Locator**。
            2.  在操作中增加一个显式的等待：`await page.waitForTimeout(5000);` (不推荐，但有时有用)。
            3.  增加 `playwright.config.ts` 中的 `timeout` 或 `actionTimeout` 配置。
      * **Element not visible/clickable (元素不可见/不可点击):**
          * **原因:** 元素被其他元素遮挡，或尚未完成动画。
          * **解决方法:** Playwright 默认会进行操作前的可见性/可点击性检查，如果仍失败，可尝试使用 `force: true` 选项绕过检查（仅在确定元素存在时使用）。
  * 检查顺序
    1.  **运行 `npx playwright test --headed`：** 以“有头”模式运行，直观观察测试在浏览器中的执行过程。
    2.  **使用 `page.pause()`：** 在测试代码中添加 `await page.pause()`，测试将暂停，并打开 Playwright Inspector，您可以在 Inspector 中手动尝试操作和验证选择器。
    3.  **检查 Trace Viewer：** 测试失败后，打开 Trace Viewer (`npx playwright show-report`)，查看详细的步骤记录、截图、DOM 快照和网络日志。

-----

## 9\. 最佳实践（Best Practices）

  * 推荐的工作流
    1.  **使用 `Codegen` 快速生成测试骨架：** `npx playwright codegen https://your-site.com`。
    2.  **重构为 POM：** 将生成的测试代码中的选择器和操作移动到 Page Object Model 类中。
    3.  **使用 `getByRole` 等语义化 Locator：** 优先使用 `page.getByRole('button', { name: 'Submit' })` 而不是复杂的 XPath 或 CSS 选择器，提高测试的健壮性和可读性。
    4.  **并行化：** 利用 Playwright 内置的并行能力 (Worker 数量)，加速整个测试套件的运行。
  * 最常见的踩坑
      * **使用 `waitForTimeout()` 进行硬等待：** 尽量避免使用，因为它会不必要地延长测试时间。应依赖 Playwright 的**自动等待**和 `page.waitForSelector()` 或 `expect(locator).toBeVisible()` 等智能等待方法。
      * **选择器过于脆弱：** 避免使用基于动态 ID 或长串 CSS 路径的选择器。优先使用 `data-testid` 或语义化选择器。
  * 性能/安全注意点
      * **环境隔离：** 始终在测试环境或临时环境中运行 E2E 测试，不要在生产环境上执行破坏性操作。
      * **Mock/Stub 网络请求：** 对于耗时或不稳定的网络请求（如第三方 API），使用 `page.route()` 进行 Mock 或 Stub，使测试更快速、更稳定。

-----

## 10\. 资源与参考（Resources）

  * 官方文档
      * 🌐 [**Playwright 官方文档**](https://playwright.dev/docs/intro) (入门、API 参考、配置最全的资源)
  * 视频/博客
      * 📺 [**Playwright YouTube 频道**](https://www.google.com/search?q=https://www.youtube.com/%40Playwright) (官方教程、更新介绍)
      * 📰 **"Why Playwright is the future of E2E testing"** (搜索相关博客文章，了解其优势)
  * GitHub 示例
      * 🐙 [**Playwright 官方 GitHub 仓库**](https://github.com/microsoft/playwright) (查看源码和提交 issue)

-----

## 11\. 个人笔记（Personal Notes）

  * 使用心得
      * **Trace Viewer 是神！** 每次失败都应该先打开 Trace Viewer，它提供的录像、DOM 快照和网络日志能瞬间定位问题。
      * **善用 `page.route()`**：通过拦截请求和响应，可以模拟不同的后端状态，无需等待后端开发完成即可测试前端逻辑。
  * 自定义小技巧
      * **使用自定义 Fixture 封装登录状态：** 创建一个 `authenticatedPage` 夹具，在测试开始前完成登录并保存状态，后续测试直接使用，避免重复登录的开销。
  * 后续学习方向
      * **Component Testing：** 学习如何使用 Playwright 进行 React/Vue 等组件级别的测试。
      * **Visual Regression Testing：** 结合像 `playwright-visual-regression` 这样的库进行视觉回归测试。
      * **CI/CD 集成：** 学习如何在 GitHub Actions/GitLab CI 等平台上自动化运行 Playwright 测试。

[[playwright命令速查表]]
