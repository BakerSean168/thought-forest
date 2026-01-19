---
tags:
  - tech/ops/microservice
  - type/concept
  - status/seed
description: Nacos服务发现与配置管理平台
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DevOps MOC]] | [[Java MOC]]

---


# Nacos 文档

## 🧠 基础概念

- **Nacos（Dynamic Naming and Configuration Service）** 是阿里巴巴开源的服务发现与配置管理平台。
- 支持 **服务注册与发现**、**动态配置管理**、**DNS 与 HTTP 接口**。
- 适用于微服务架构，兼容 Spring Cloud、Dubbo 等主流框架。

## 🛠 使用指南

1. **安装启动**
   - 下载：[Nacos 官网](https://nacos.io)
   - 启动命令：`sh startup.sh -m standalone`（单机模式）

2. **服务注册与发现**
   - 客户端通过 HTTP 或 SDK 注册服务。
   - 服务消费者通过接口获取服务列表，实现负载均衡。

3. **配置管理**
   - 支持配置热更新。
   - 配置格式支持：Properties、YAML、JSON 等。
   - 可通过控制台或 API 管理配置。

4. **权限与分组**
   - 支持命名空间、分组、标签等机制，便于多环境隔离。

## 🧪 实战经验

- **配置降级**：使用本地缓存避免配置中心不可用时影响服务。
- **服务健康检查**：定期检查服务状态，避免调用异常实例。
- **灰度发布**：结合分组和标签实现灰度策略。
- **持久化存储**：推荐使用 MySQL 替代默认嵌入式数据库。

## 📌 经验总结

- 配置项命名要规范，便于维护。
- 服务注册后需关注心跳机制，避免服务下线。
- 配置变更需做好版本控制和回滚策略。
- 建议结合 Spring Cloud Alibaba 使用，生态更完整。

## 🔍 信息参考

- 官方文档：[https://nacos.io](https://nacos.io)
- GitHub 项目：[https://github.com/alibaba/nacos](https://github.com/alibaba/nacos)
- 社区教程：掘金、CSDN、博客园等平台

