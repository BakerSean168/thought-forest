---
tags:
  - tech/dev/database
  - type/concept
  - status/seed
description: Neon（PostgreSql云部署）
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---


# Neon（PostgreSQL 云部署）

## 基础概念

Neon 是一款专注于 PostgreSQL 的无服务器（Serverless）云数据库服务，主打**开源、轻量、按需付费**，核心架构采用“存储与计算分离”设计，适合开发者快速部署、低成本使用 PostgreSQL 数据库。其核心概念包括：

1. **Project（项目）**  
   Neon 的顶层组织单位，一个 Project 包含所有数据库资源（如数据库、分支、用户、连接串等），是资源管理的基本单元。每个 Project 对应一个独立的 PostgreSQL 集群环境。

2. **Branch（分支）**  
   Neon 的特色功能，支持对数据库进行**零复制成本的分支操作**（类似 Git 分支）。分支可独立于主库运行，适合开发、测试场景（如基于生产数据创建测试分支，不影响主库），分支间数据隔离，可随时合并或删除。

3. **Database（数据库）**  
   基于 PostgreSQL 引擎的实际数据库实例，每个 Project 可创建多个数据库，共享底层存储资源。

4. **Role（角色/用户）**  
   数据库访问账户，支持自定义权限（如只读、读写、管理员），通过角色控制对数据库的访问。

5. **Connection String（连接串）**  
   用于连接数据库的字符串，包含主机、端口、数据库名、用户名、密码等信息，支持多种连接方式（如 psql 命令行、应用程序驱动）。

6. **Serverless 架构**  
   计算资源按需分配，闲置时自动休眠（不产生费用），请求触发时快速唤醒，适合流量波动大的场景，降低资源浪费。

7. **Storage（存储）**  
   独立于计算节点的分布式存储，自动备份、加密，支持按实际使用量计费，无需预先分配存储容量。

## 使用指南

### 1. 注册与创建项目

- **注册账号**：访问 [Neon 官网](https://neon.tech/)，通过邮箱或 GitHub 账号注册，免费计划无需信用卡。
- **创建 Project**：登录后点击“New Project”，输入项目名称（如“my-first-neon-db”），选择区域（如“US East”），默认生成一个主分支（main）和初始数据库（neondb）。

### 2. 管理数据库与分支

- **创建数据库**：在 Project 页面，进入“Databases”标签，点击“New Database”，输入名称（如“app_db”），选择所属分支（默认 main），自动关联默认角色。
- **创建分支**：在“Branches”标签，点击“New Branch”，可选择基于主分支（main）或其他分支创建，支持指定分支名称（如“dev-branch”），创建后自动生成独立的连接串。
- **切换分支**：通过分支列表选择目标分支，查看其数据库、连接信息，或进行删除、合并操作（合并需手动处理冲突）。

### 3. 连接数据库

- **获取连接串**：在 Project 主页或分支详情页，找到“Connection String”，格式如下：  
  `postgres://[用户名]:[密码]@[主机名].[区域].neon.tech:5432/[数据库名]`  
  （默认用户为“neon”，密码可在“Roles”标签重置）。
- **通过 psql 连接**：  
  在终端运行：  

  ```bash
  psql "postgres://neon:your_password@ep-cool-darkness-12345.us-east-2.aws.neon.tech:5432/app_db"
  ```

- **通过应用程序连接**：  
  以 Python 为例（使用 `psycopg2`）：  

  ```python
  import psycopg2
  conn = psycopg2.connect(
      host="ep-cool-darkness-12345.us-east-2.aws.neon.tech",
      database="app_db",
      user="neon",
      password="your_password",
      port="5432"
  )
  ```

- **通过 GUI 工具连接**：支持 DBeaver、DataGrip 等工具，输入连接串信息即可。

### 4. 数据备份与恢复

- **自动备份**：Neon 免费计划默认开启每日自动备份，备份数据保留 7 天（付费计划可延长）。
- **时间点恢复（PITR）**：在“Branches”标签，选择“New Branch from Point in Time”，指定日期和时间，可基于历史数据创建新分支，实现数据恢复。

### 5. 权限管理

- **创建角色**：在“Roles”标签，点击“New Role”，输入用户名和密码，可指定权限（如 `LOGIN`、`CREATEDB` 等）。
- **分配权限**：通过 SQL 命令为角色授权，例如：  

  ```sql
  GRANT SELECT, INSERT ON table_name TO new_role;
  ```

## 信息参考

### 免费计划限制

- 存储：最多 0.5GB
- 分支：最多 10 个
- 连接：最多 3 个并发连接
- 自动备份：保留 7 天
- 区域：部分免费区域（如 US East、EU West）

### 付费计划特点

- 更高存储容量（最高 10TB+）
- 无限分支和并发连接
- 更长备份保留期（最多 30 天）
- 支持私有连接、读写分离等高级功能

### 官方资源

- 官网：[https://neon.tech/](https://neon.tech/)
- 文档：[Neon Docs](https://neon.tech/docs)（包含快速入门、API 参考、分支管理等详细指南）
- 社区：[Neon Discord](https://discord.gg/neontech)（技术支持与开发者交流）
- 开源仓库：[github.com/neondatabase/neon](https://github.com/neondatabase/neon)（核心代码开源，可自建部署）

### 适用场景

- 开发/测试环境（分支功能适合多环境隔离）
- 小型应用或原型项目（免费计划足够支撑）
- 流量波动大的应用（Serverless 特性节省成本）
- 需要快速迭代的团队（零成本分支复制加速开发）

Neon 凭借轻量化、低成本和 Git 式分支管理，成为开发者使用 PostgreSQL 的热门选择，尤其适合注重敏捷开发和成本控制的场景。
