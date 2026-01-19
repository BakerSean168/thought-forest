---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: Tauri
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[前端套壳桌面应用程序]] | [[Rust]]

---


# Tauri

## 基础概念

对比 Electron：
- 渲染层：同样使用系统 WebView (Edge WebView2 / WebKit / WebKitGTK)；Electron 自带 Chromium
- 主进程：Rust (Tauri Core) vs Node.js
- 体积：Tauri 可 <10MB；Electron 通常 >60MB
- 资源占用：无捆绑浏览器，内存更低
- 安全：允许明确白名单 API；默认最小权限

核心组成：
1. Rust 后端（命令执行、文件系统、安全、防逃逸）
2. 前端（任意 SPA/MPA：React/Vue/Svelte/Vite 构建）
3. IPC 通信（invoke/emit）
4. 插件系统（通知、更新、窗口、多线程等）
5. 安全配置 tauri.conf.json（权限、白名单、协议、自定义 scheme）

适用：跨平台桌面工具、轻量客户端、内网工具、嵌入式 UI。

不适用：需要深度 Chromium 定制、复杂多媒体处理（需自扩展）。

安全原则：默认禁用危险 API（fs/network/shell），需要显式开启；支持 CSP、协议隔离、签名更新。

## 使用指南

### 初始化
```bash
pnpm create vite my-tauri --template react-ts
cd my-tauri
pnpm add -D @tauri-apps/cli
pnpm tauri init
```

项目结构（关键）：
```
src/            # 前端代码
src-tauri/
  tauri.conf.json
  src/
    main.rs     # Rust 入口
```

### 定义 Rust 命令
```rust
// src-tauri/src/main.rs
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]
use tauri::command;

#[command]
fn add(a: i32, b: i32) -> i32 { a + b }

fn main() {
  tauri::Builder::default()
    .invoke_handler(tauri::generate_handler![add])
    .run(tauri::generate_context!())
    .expect("error running tauri application");
}
```

前端调用：
```ts
import { invoke } from '@tauri-apps/api/tauri'
const result = await invoke<number>('add', { a: 1, b: 2 })
```

### 窗口管理
多窗口：`app.create_window()`（Rust 或 JS API）；可设透明/置顶/无边框；注意在前端控制窗口生命周期防止内存泄露。

### 文件系统 & 安全
通过安全作用域（fs allowlist）限制访问路径；建议所有文件写入放在 `AppData` 目录，并且校验用户输入避免目录穿越。

### 自动更新
使用内置 updater 或自建服务：配置 `tauri.conf.json > updater`，签名保证完整性。

### 打包
```bash
pnpm tauri build
```
输出安装包（msi / dmg / AppImage 等）。

### 常见插件
- `notification` 本地系统通知
- `dialog` 文件对话框
- `shell` 受控执行命令（需小心注入）
- `updater` 自动更新
- 社区第三方：window-state / single-instance / sql 等

## 实战经验

1. 性能：前端构建产物尽量 Tree-Shaking + 使用 wasm (可选) 加速算法
2. IPC 频率控制：批量数据通过序列化文件中转，避免大量小 invoke
3. 日志：Rust tracing + 前端 console；生产下写文件需用户授权路径
4. 崩溃恢复：关键状态持久化（IndexedDB / sqlite / local file）
5. 权限最小化：tauri.conf.json 仅开启需要的 API；动态场景拆分命令
6. 防注入：前端不拼接 shell；Rust 端命令参数严格校验
7. 异步任务：Rust 线程池 / tokio runtime；避免阻塞主线程
8. 前端调试：devtools 仅在开发模式允许，禁用生产
9. 自动更新：预发布渠道 + hash 校验
10. 多平台差异：路径、权限、系统托盘差异分支处理

常见坑：
- invoke 名称不一致 -> 404
- WebView 版本差异导致兼容性问题（尤其旧 Windows）
- 大文件读写卡顿 UI（需后台线程 + 进度事件）
- 不正确释放窗口引用导致内存不回收

### 运行报错

#### `linking with `link.exe` failed: exit code: 1120  `

这是一个链接器错误，问题出现在ONNX Runtime (libort_sys)库的链接阶段。错误显示缺少一些C++标准库的符号，这通常是由于C++运行时库版本不匹配或者编译器配置问题导致的。

`$env:RUSTFLAGS = "-C target-feature=+crt-static"; $env:CXX = "cl.exe"; $env:CC = "cl.exe"; cd "d:\workProgram\hyprnote\apps\desktop"; pnpm tauri:dev`

## 经验总结

核心策略：Rust 负责性能敏感 + 系统访问；前端负责 UI/交互。最小化命令接口面，保证可测试性与演化弹性。

安全/性能双优：限制可执行命令 + 合理 IPC 批处理 + 使用预编译资源减少启动时间。

## 信息参考

- 官方: <https://tauri.app>
- API 文档: <https://tauri.app/v1/api>
- 安全模型: <https://tauri.app/v1/guides/security>
- 插件生态: <https://github.com/tauri-apps>
- VS Code 插件：Tauri Console / Rust Analyzer

