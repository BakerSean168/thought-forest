---
tags:
  - tech/lang/typescript
  - type/howto
  - status/growing
description: playwright命令速查表
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[ECMAScript MOC]] | [[前端基础 MOC]]

---


# playwright命令速查表



## Playwright 命令速查表 (CLI Cheatsheet) 🚀

|**类别**|**命令**|**描述**|**常用选项**|
|---|---|---|---|
|**初始化**|`npm init playwright@latest`|交互式地初始化一个新的 Playwright 项目。|`-y` (跳过提问，使用默认配置)|
|**安装**|`npx playwright install`|安装或重新安装 Playwright 所需的浏览器二进制文件（Chromium, Firefox, WebKit）。|`chromium` / `firefox` / `webkit` (指定安装特定浏览器)|
|**运行测试**|`npx playwright test`|运行项目配置中定义的所有测试。|`--project [name]` (只运行特定配置，如 `chromium`)|
|**运行特定文件**|`npx playwright test [path]`|运行指定路径下的所有测试文件。|`--headed` (以“有头”模式运行，方便观察)|
|**运行特定测试**|`npx playwright test [path]:[line]`|运行特定文件中的特定测试（通常使用文件名和行号）。|`-g "regex"` (只运行名称匹配正则的测试)|
|**更新快照**|`npx playwright test --update-snapshots`|更新所有失败的（或新的）视觉回归测试快照。|N/A|
|**调试/排错**|`npx playwright test --debug`|运行测试并自动启动 Playwright Inspector，方便逐行调试。|N/A|
|**录制代码**|`npx playwright codegen [url]`|启动一个浏览器实例并录制用户操作，自动生成测试代码。|`--output [file.ts]` (将录制代码保存到文件)|
|**显示报告**|`npx playwright show-report`|打开上一次测试运行生成的 HTML 报告 (Trace Viewer)。|N/A|
|**清除缓存**|`npx playwright cache clean`|清除 Playwright 下载的浏览器二进制文件缓存。|N/A|

### 常用运行选项 (Common Options)

|**选项**|**描述**|**示例**|
|---|---|---|
|**`-n [num]`**|设置测试的运行次数。|`npx playwright test -n 3`|
|**`--workers [num]`**|设置并行运行的 Worker 进程数。|`npx playwright test --workers 4`|
|**`--retries [num]`**|设置失败测试的重试次数。|`npx playwright test --retries 2`|
|**`--timeout [ms]`**|设置测试的默认超时时间（毫秒）。|`npx playwright test --timeout 60000`|
|**`--grep "keyword"`**|只运行测试名称中包含指定关键词的测试。|`npx playwright test --grep "login"`|
|**`--max-fails [num]`**|设置达到多少次失败后停止测试运行。|`npx playwright test --max-fails 5`|
|**`--browser [name]`**|仅运行在指定浏览器上 (`chromium`/`firefox`/`webkit`)。|`npx playwright test --browser chromium`|

---

### 调试专用命令 (Debugging Helpers)

在代码中添加以下调用，可以更精细地控制调试流程：

|**代码调用**|**作用**|**对应 CLI 调试模式**|
|---|---|---|
|`await page.pause();`|暂停测试执行，打开 Inspector，可以在控制台手动操作。|`npx playwright test` (需在代码中添加)|
|`test.only(...)`|临时只运行一个或一组测试，用于聚焦调试。|`npx playwright test`|


