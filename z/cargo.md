---
tags:
  - tech/dev/programming
  - type/concept
  - status/seed
description: cargo
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Rust]] | [[后端开发 MOC]]

---


# Cargo

## 基础概念

### 什么是 Cargo？

Cargo 是 Rust 语言的官方构建系统和包管理器，类似于 Node.js 的 npm、Python 的 pip 或 Ruby 的 bundler。它负责以下核心功能：

1. **项目创建**：初始化新项目结构和模板
2. **依赖管理**：下载和编译项目依赖项
3. **构建系统**：编译代码、运行测试和生成文档
4. **发布工具**：打包和发布 crate 到 crates.io

### Cargo 核心组件

1. **Cargo.toml**：项目清单文件，包含项目元数据和依赖信息
2. **Cargo.lock**：精确的依赖版本锁定文件（类似 package-lock.json）
3. **Target 目录**：存放构建产物的目录
4. **Registry**：默认使用 crates.io 作为包注册中心

### Cargo 工作流程

1. 读取配置（Cargo.toml）
2. 解析依赖关系图
3. 下载缺失的依赖项
4. 编译依赖项（必要时）
5. 编译项目代码
6. 运行测试（如需要）
7. 生成文档（如需要）

## 使用指南

### 安装与配置

Cargo 随 Rust 工具链自动安装，可通过 rustup 安装：

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

验证安装：

```bash
cargo --version
```

### 常用命令

#### 项目创建与管理

```bash
cargo new my_project# 创建二进制项目
cargo new --lib my_lib# 创建库项目
cargo init# 在当前目录初始化项目
```

#### 构建与运行

```bash
cargo build# 调试构建
cargo build --release# 发布构建
cargo run# 构建并运行
cargo check# 快速检查代码（不生成可执行文件）
```

#### 依赖管理

```bash
cargo add package_name# 添加依赖（需要安装 cargo-edit）
cargo update# 更新依赖
cargo tree# 查看依赖树
```

#### 测试与文档

```bash
cargo test# 运行测试
cargo bench# 运行基准测试
cargo doc# 生成文档
cargo doc --open# 生成并打开文档
```

#### 发布与安装

```bash
cargo publish# 发布到 crates.io
cargo install crate_name# 安装二进制 crate
cargo uninstall crate_name # 卸载二进制 crate
```

### Cargo.toml 详解

典型结构示例：

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"# Rust 版本

[dependencies]
serde = "1.0"# 精确版本
tokio = { version = "1.0", features = ["full"] } # 带特性配置

[dev-dependencies] # 开发依赖
mockall = "0.11"

[build-dependencies] # 构建脚本依赖
cc = "1.0"
```

## 实战经验

### 多环境配置管理

```toml
# .cargo/config.toml
[target.'cfg(unix)']
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[profile.dev]
opt-level = 1# 开发环境优化级别1

[profile.release]
lto = true# 发布环境启用链接时优化
```

### 工作区（Workspace）配置

```toml
# 根目录 Cargo.toml
[workspace]
members = [
"crates/core",
"crates/cli",
"crates/web",
]
resolver = "2"# 使用新版特性解析器
```

### 条件编译与特性

```toml
[features]
default = ["sqlite"]# 默认特性
sqlite = ["rusqlite"] # 启用sqlite支持
postgres = ["tokio-postgres"] # 启用postgres支持

[dependencies]
rusqlite = { version = "0.27", optional = true }
tokio-postgres = { version = "0.7", optional = true }
```

### 自定义构建脚本

```rust
// build.rs
fn main() {
println!("cargo:rerun-if-changed=src/headers.h");
println!("cargo:rustc-link-lib=static=mylib");
}
```

### 性能优化技巧

1. **增量编译**：默认启用，可通过环境变量控制 `CARGO_INCREMENTAL=1`
2. **并行编译**：`CARGO_BUILD_JOBS=$(nproc)`
3. **依赖缓存**：使用 `sccache` 加速重复编译
4. **选择性测试**：`cargo test module_name::test_case`

## 经验总结

### 最佳实践

1. **版本管理**：
- 遵循语义化版本控制（SemVer）
- 使用 `^` 兼容性标记（默认行为）
- 重要依赖可考虑精确版本锁定

2. **依赖优化**：
- 最小化依赖项（`cargo tree -d` 查找重复依赖）
- 使用 `--no-default-features` 减少不必要的特性
- 定期运行 `cargo outdated` 检查过时依赖

3. **构建优化**：
- 开发时使用 `cargo check` 快速验证
- 合理拆分 crate 以减少重编译
- 考虑使用 workspace 管理大型项目

4. **发布准备**：
- 运行 `cargo publish --dry-run` 测试发布
- 使用 `cargo package --list` 检查包含文件
- 注意 `.cargoignore` 类似 `.gitignore`

### 常见问题解决

1. **编译卡顿**：
- 检查是否开启了过多的特性
- 尝试删除 `target` 目录重新构建
- 考虑使用 `mold` 或 `lld` 替代默认链接器

2. **版本冲突**：
- 使用 `cargo tree -d` 查找版本冲突
- 考虑使用 `[patch]` 或 `[replace]` 临时覆盖

3. **下载缓慢**：
- 配置国内镜像源（如中科大源）
- 设置 `CARGO_HTTP_MULTIPLEXING=false` 尝试解决

4. **内存不足**：
- 减少并行编译任务 `CARGO_BUILD_JOBS=2`
- 增加交换空间

## 信息参考

### 官方资源

1. [Cargo 官方手册](https://doc.rust-lang.org/cargo/)
2. [Cargo 命令参考](https://doc.rust-lang.org/cargo/commands/index.html)
3. [Crates.io 注册中心](https://crates.io)
4. [Rust 版本指南](https://doc.rust-lang.org/edition-guide/)

### 实用工具

1. **cargo-edit**：扩展 `cargo add/rm/upgrade` 命令
2. **cargo-audit**：检查安全漏洞
3. **cargo-watch**：文件变化自动重建
4. **cargo-expand**：宏展开查看器
5. **cargo-bloat**：分析二进制体积

### 学习进阶

1. [《The Cargo Book》中文版](https://cargo.budshome.com/)
2. [Rust 性能优化指南](https://github.com/rust-lang/rustc-perf/blob/master/collector/README.md)
3. [Cargo 内部机制解析](https://github.com/rust-lang/cargo/tree/master/src/doc/src)
4. [Cargo 插件开发指南](https://doc.rust-lang.org/cargo/reference/external-tools.html)

通过掌握 Cargo 的全面知识，你可以显著提升 Rust 开发效率，更好地管理项目依赖，并优化构建过程。随着 Rust 生态的发展，Cargo 也在持续演进，建议定期关注官方更新日志以获取最新特性。
