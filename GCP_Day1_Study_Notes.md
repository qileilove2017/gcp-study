# GCP 学习路线 - Day 1

## Understanding GCP Resource Hierarchy and Project Design

### 学习目标

Day 1 不学习具体服务，而是建立 GCP 的基础认知：

- GCP 资源层级结构
- Organization、Folder、Project 的关系
- Project 的真正作用
- 企业级 Project 设计最佳实践
- GCP 与 Azure、AWS 的核心区别

---

# 1. GCP Resource Hierarchy

```text
Organization
    │
    ├── Folder
    │      │
    │      ├── Project
    │      │      ├── GKE
    │      │      ├── Cloud SQL
    │      │      ├── Storage
    │      │      └── Service Account
    │      │
    │      └── Project
    │
    └── Folder
```

## Organization

企业在 GCP 中的最高管理单元。

例如：

- 企业集团
- SaaS 公司
- 银行
- 电商平台

通常与企业域名关联。

---

## Folder

用于组织部门、业务线或团队。

例如：

```text
Enterprise Organization

├── Digital Business
├── Security
├── Infrastructure
├── Operations
└── Data Platform
```

Folder 主要用于权限继承和组织管理。

---

## Project

GCP 最重要的资源容器。

所有资源最终都属于某个 Project。

例如：

```text
payment-platform-prod
customer-service-prod
analytics-platform-prod
```

Project 中可以包含：

- GKE
- Cloud SQL
- Cloud Storage
- Load Balancer
- Service Account
- Monitoring
- Logging

---

# 2. Azure、AWS、GCP 对比

## Azure

```text
Tenant
 └── Subscription
      └── Resource Group
           └── Resources
```

### 特点

Subscription：

- 权限边界
- 配额边界
- 计费边界

Resource Group：

- 逻辑分组
- 生命周期管理

---

## AWS

```text
Organization
 └── Account
      └── Resources
```

Account：

- 权限边界
- 计费边界
- 配额边界

---

## GCP

```text
Organization
 └── Folder
      └── Project
           └── Resources
```

---

# 3. 最重要的知识点

很多 Azure 工程师会认为：

```text
Project ≈ Resource Group
```

这是错误的。

正确理解：

```text
Project ≈ Subscription
```

因为 Project 拥有：

- 独立 IAM
- 独立 Billing
- 独立 Quota
- 独立 Audit
- 独立 Monitoring

---

# 4. Security Boundary

Security Boundary（安全边界）表示：

- 权限隔离
- 成本隔离
- 配额隔离
- 审计隔离

发生的位置。

Azure：

```text
Subscription
```

AWS：

```text
Account
```

GCP：

```text
Project
```

---

# 5. 企业最佳实践

## One Environment One Project

推荐：

```text
payment-platform-dev
payment-platform-sit
payment-platform-uat
payment-platform-prod
```

而不是：

```text
payment-platform

├── dev
├── sit
├── uat
└── prod
```

原因：

- 权限隔离
- 日志隔离
- 审计隔离
- 成本隔离

---

## One Application One Project

推荐：

```text
payment-platform-prod
analytics-platform-prod
customer-service-prod
```

不推荐：

```text
prod-project

├── payment-platform
├── analytics-platform
└── customer-service
```

原因：

多个系统共享 IAM、Quota、Billing，治理困难。

---

# 6. 企业场景示例

```text
Organization
│
└── Enterprise-Org
     │
     └── Digital-Business
            │
            ├── payment-platform-dev
            ├── payment-platform-sit
            ├── payment-platform-uat
            ├── payment-platform-preprod
            └── payment-platform-prod
```

每个 Project 可以独立拥有：

```text
GKE
Cloud SQL
Artifact Registry
Cloud Storage
Cloud Monitoring
Cloud Logging
Service Account
```

---

# 7. Day1 核心记忆点

### 记忆点1

Project 不是 Resource Group。

Project 更接近 Azure Subscription。

### 记忆点2

Project 是 Security Boundary。

### 记忆点3

Project 是 Billing Boundary。

### 记忆点4

Project 是 Quota Boundary。

### 记忆点5

企业最佳实践：

```text
One Application
One Environment
One Project
```

---

# 面试高频问题

## Q1

Project 最接近 Azure 什么概念？

答案：

```text
Subscription
```

---

## Q2

为什么 Project 不是 Resource Group？

因为 Project 拥有：

- IAM
- Billing
- Quota
- Audit

这些能力。

---

## Q3

GCP 中最重要的安全边界是什么？

答案：

```text
Project
```

---

## Q4

为什么企业通常一个环境一个 Project？

实现：

- 权限隔离
- 配额隔离
- 日志隔离
- 审计隔离
- 成本隔离

---

# Day1 总结

最重要的一句话：

> In Google Cloud Platform, Project is the fundamental security, billing, quota, and management boundary.

翻译：

在 GCP 中，Project 是最核心的安全、计费、配额和管理边界。
