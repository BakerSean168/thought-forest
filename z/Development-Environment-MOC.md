---
tags:
  - type/moc
  - status/growing
description: 各编程语言的开发环境配置、工具链、包管理与常用库索引
created: 2025-12-09T10:00:00
updated: 2025-12-09T10:00:00
---

# 💻 开发环境 MOC

> 各编程语言的开发环境配置、包管理器、开发工具、核心库与框架速查

---

## JavaScript / TypeScript

### 环境与运行时
| 资源 | 说明 |
|------|------|
| [[Node.js MOC]] | Node.js 运行时与开发 |
| [[Deno]] | 现代 JS 运行时（如有笔记） |

### 包管理与版本管理
| 工具 | 文档 | 说明 |
|------|------|------|
| **npm** | [[Nodejs的包管理器]] | Node 官方包管理器 |
| **pnpm** | [[Nodejs的包管理器]] | 高效包管理器 |
| **yarn** | 快速可靠的包管理器 |
| **nvm** | [[Nodejs的包管理器]] | Node 版本管理 |
| **fnm** | Fast Node Manager |
| **corepack** | [[corepack]] | Node 官方包管理器管理工具 |

### 构建工具
| 工具 | 文档 | 说明 |
|------|------|------|
| **Webpack** | 模块打包器 |
| **Vite** | [[Vite-MOC]] | 极速构建工具 |
| **Rollup** | [[rollup]] | ES 模块打包 |
| **Parcel** | 零配置打包工具 |
| **esbuild** | 超快 JS 打包器 |
| **tsup** | [[tsup]] | TypeScript 打包工具 |

### 框架与库
| 框架/库 | 文档 | 用途 |
|------|------|------|
| **React** | [[React MOC]] | UI 框架 |
| **Vue** | [[Vue3 MOC]] | 渐进式框架 |
| **Angular** | 企业框架 |
| **Next.js** | React SSR 框架 |
| **Nuxt** | Vue SSR 框架 |
| **Svelte** | 编译式框架 |
| **Astro** | 静态站点生成器 |

### 工具库
| 库 | 文档 | 说明 |
|------|------|------|
| **Lodash** | [[Lodash]] | JS 工具函数库 |
| **date-fns** | [[date-fns 库的使用]] | 日期处理库 |
| **axios** | [[axios]] | HTTP 客户端 |
| **winston** | [[winston]] | 日志库 |
| **dotenv** | 环境变量管理 |

### 测试框架
| 框架 | 文档 | 说明 |
|------|------|------|
| **Jest** | 单元测试框架 |
| **Vitest** | [[Vitest 工具]] | Vite 原生测试框架 |
| **Mocha** | 灵活测试框架 |
| **Cypress** | E2E 测试框架 |
| **Playwright** | [[playwright]] | 跨浏览器自动化 |

### 开发工具
| 工具 | 文档 | 说明 |
|------|------|------|
| **TypeScript** | [[TypeScript]] | 类型检查 |
| **ESLint** | 代码检查 |
| **Prettier** | 代码格式化 |
| **Husky** | [[Husky-lint-staged]] | Git Hook 工具 |
| **TurboRepo** | [[Turborepo]] | Monorepo 编译器 |
| **Nx** | [[Nx MOC]] | Monorepo 构建框架 |

---

## Python

### 环境与版本管理
| 工具 | 文档 | 说明 |
|------|------|------|
| **Python** | [[Python MOC]] | Python 语言 |
| **Pyenv** | Python 版本管理 |
| **Virtualenv** | [[virtualenv]] | 虚拟环境工具 |
| **Poetry** | 现代依赖管理 |
| **Pipenv** | 开发环境管理 |

### 包管理
| 工具 | 说明 |
|------|------|
| **pip** | Python 包管理器 |
| **pip-tools** | 依赖版本管理 |
| **PyPI** | Python 包仓库 |

### Web 框架
| 框架 | 文档 | 说明 |
|------|------|------|
| **Django** | 全能 Web 框架 |
| **FastAPI** | [[FastAPI]] | 现代异步框架 |
| **Flask** | 微型框架 |
| **Fastly** | 性能框架 |

### 数据科学与机器学习
| 库 | 说明 |
|------|------|
| **NumPy** | 数值计算 |
| **Pandas** | 数据分析 |
| **Scikit-learn** | 机器学习 |
| **PyTorch** | [[PyTorch]] | 深度学习框架 |
| **TensorFlow** | 深度学习框架 |
| **Matplotlib** | 数据可视化 |

### 任务队列
| 库 | 说明 |
|------|------|
| **Celery** | [[Celery]] | 分布式任务队列 |
| **RQ** | 简单任务队列 |

### 开发工具
| 工具 | 文档 | 说明 |
|------|------|------|
| **pytest** | 测试框架 |
| **Black** | 代码格式化 |
| **Flake8** | 代码检查 |
| **mypy** | 类型检查 |

---

## Java

### 环境与版本
| 工具 | 文档 | 说明 |
|------|------|------|
| **JDK** | Java 开发工具包 |
| **SDKMAN** | Java 版本管理 |
| **Maven** | [[maven]] | 构建管理工具 |
| **Gradle** | 现代构建工具 |

### 框架与库
| 框架 | 说明 |
|------|------|
| **Spring Boot** | [[SpringBoot]] | 企业级框架 |
| **Hibernate** | ORM 框架 |
| **MyBatis** | SQL 映射框架 |
| **Dubbo** | [[Dubbo]] | RPC 框架 |
| **Nacos** | [[nacos]] | 服务治理平台 |

### 测试
| 框架 | 说明 |
|------|------|
| **JUnit** | 单元测试 |
| **Mockito** | Mock 库 |

---

## Go

### 环境与管理
| 工具 | 文档 | 说明 |
|------|------|------|
| **Go** | [[Go MOC]] | Go 语言与开发 |
| **Go Modules** | 依赖管理 |
| **GoVersion** | 版本管理 |

### Web 框架
| 框架 | 说明 |
|------|------|
| **Gin** | 高性能 Web 框架 |
| **Echo** | 轻量级框架 |
| **gRPC** | [[gRPC]] | RPC 框架 |

### 常用库
| 库 | 说明 |
|------|------|
| **GORM** | ORM 库 |
| **sqlc** | SQL 类型安全 |

---

## Rust

### 环境与工具
| 工具 | 文档 | 说明 |
|------|------|------|
| **Rust** | [[Rust]] | Rust 语言 |
| **rustup** | [[Rustup]] | Rust 版本管理 |
| **cargo** | [[cargo]] | 构建工具 |

### Web 框架
| 框架 | 说明 |
|------|------|
| **Actix-web** | 高性能异步框架 |
| **Tokio** | 异步运行时 |
| **Serde** | 序列化库 |

---

## 其他语言

### C/C++
| 工具 | 说明 |
|------|------|
| **CMake** | 构建系统 |
| **GCC/Clang** | 编译器 |
| **Vcpkg** | 包管理器 |

### PHP
| 工具 | 文档 | 说明 |
|------|------|------|
| **PHP** | [[phpEnvironmentInstall]] | PHP 环境配置 |
| **Composer** | 包管理器 |
| **Laravel** | Web 框架 |

### SQL
| 工具 | 文档 | 说明 |
|------|------|------|
| **MySQL** | [[Mysql]] | 关系数据库 |
| **PostgreSQL** | 高级关系数据库 |
| **SQLite** | [[SQLite]] | 轻量级数据库 |
| **Redis** | [[Redis]] | 内存数据库 |
| **MongoDB** | 文档数据库 |

---

## 🔗 相关资源

- [[Resource]] - 工具、平台、在线服务汇总
- [[前端基础 MOC]] - 前端工具链详解
- [[后端开发 MOC]] - 后端框架与最佳实践
- [[DevOps MOC]] - CI/CD 与部署工具

---

*最后更新: 2025-12-09*
