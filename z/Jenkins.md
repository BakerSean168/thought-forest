---
tags:
  - tech/ops/devops
  - type/concept
  - status/growing
description: Jenkins
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[DevOps MOC]] | [[Resource]]

---


# Jenkins CI/CD 自动化服务器

## 基础概念

Jenkins 是一款开源 CI/CD 软件，用于自动化各种任务，包括构建、测试和部署软件。

**核心特性：**
- 持续集成/持续部署（CI/CD）
- 插件生态丰富（1800+ 插件）
- Pipeline as Code（Jenkinsfile）
- 分布式构建支持
- 多平台支持

---

## 安装方式

### Docker 安装（推荐）

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

### 获取初始密码

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

---

## Pipeline 基础

### Jenkinsfile 示例

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'npm run deploy'
            }
        }
    }
    
    post {
        success {
            echo '构建成功！'
        }
        failure {
            echo '构建失败！'
        }
    }
}
```

---

## 常用插件

| 插件 | 用途 |
|------|------|
| **Git** | Git 仓库集成 |
| **Pipeline** | Pipeline 支持 |
| **Docker Pipeline** | Docker 构建支持 |
| **Blue Ocean** | 现代化 UI |
| **Credentials** | 凭据管理 |
| **NodeJS** | Node.js 环境 |

---

## 最佳实践

1. **使用 Jenkinsfile** - 将 Pipeline 配置纳入版本控制
2. **分离环境** - 开发/测试/生产环境独立配置
3. **凭据管理** - 使用 Credentials 插件管理敏感信息
4. **并行构建** - 利用 parallel 加速构建
5. **构建缓存** - 缓存依赖减少构建时间

---

## 相关笔记

- [[DevOps MOC]] - DevOps 实践索引
- [[Docker MOC]] - Docker 容器化
- [[Git MOC]] - Git 版本控制
