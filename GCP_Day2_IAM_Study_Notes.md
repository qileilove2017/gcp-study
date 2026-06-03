# GCP 学习路线 - Day 2

## IAM：Identity and Access Management

---

## 学习目标

Day 2 的核心目标是理解 GCP IAM 的基本模型。

学完本节后，需要能够回答以下问题：

- 谁在访问 GCP 资源？
- 权限是如何授予的？
- 应用程序如何访问 GCP 服务？
- GKE Pod 如何访问 Cloud SQL、Cloud Storage、Secret Manager？
- Terraform Pipeline 应该使用什么身份创建资源？

IAM 是 GCP 中最重要的基础能力之一。很多企业中的权限问题、安全风险、自动化部署失败，本质上都与 IAM 设计有关。

---

# 1. IAM 的本质

IAM 主要回答三个问题：

```text
Who
Can Do What
On Which Resource
```

对应到 GCP 中就是：

```text
Principal
Role
Resource
```

也可以理解为：

```text
谁
拥有什么权限
作用在哪个资源上
```

例如：

```text
analytics-sa
    ↓
roles/storage.objectViewer
    ↓
analytics-data-bucket
```

这表示：

```text
analytics-sa 可以读取 analytics-data-bucket 中的对象
```

---

# 2. GCP IAM 的四个核心概念

GCP IAM 中最核心的四个概念是：

```text
Principal
Role
Resource
Policy
```

---

# 3. Principal：谁在访问资源

Principal 表示访问资源的身份。

常见 Principal 包括：

- User
- Group
- Service Account

---

## 3.1 User

User 表示真实用户。

例如：

```text
alice@example.com
bob@example.com
```

User 适合用于人员登录、查看资源、执行管理操作。

但生产系统不应该依赖某个 User 身份运行，因为人员可能会转岗、离职，权限也难以长期审计。

---

## 3.2 Group

Group 表示用户组。

例如：

```text
dev-team@example.com
platform-team@example.com
security-team@example.com
```

企业中更推荐将权限授予 Group，而不是直接授予单个 User。

推荐方式：

```text
Group
    ↓
Role
    ↓
Resource
```

不推荐方式：

```text
User
    ↓
Role
    ↓
Resource
```

原因是 Group 更容易管理人员变更和权限审计。

---

## 3.3 Service Account

Service Account 是 GCP IAM 中非常重要的概念。

它不是人，而是应用程序、服务、自动化任务使用的身份。

例如：

```text
payment-sa@project-id.iam.gserviceaccount.com
analytics-sa@project-id.iam.gserviceaccount.com
terraform-sa@project-id.iam.gserviceaccount.com
```

Service Account 通常用于：

- GKE 中的应用访问 GCP 服务
- Cloud Run 访问 Cloud SQL
- Terraform Pipeline 创建云资源
- CI/CD Pipeline 部署应用
- Batch Job 读取 Storage 或 Secret

---

# 4. 为什么生产系统应该使用 Service Account

假设有一个服务：

```text
payment-service
```

它需要：

```text
读取 Secret Manager
连接 Cloud SQL
读取 Cloud Storage
```

错误做法是让它使用某个员工账号：

```text
alice@example.com
```

正确做法是创建专用 Service Account：

```text
payment-sa@project-id.iam.gserviceaccount.com
```

然后给它授予最小必要权限：

```text
roles/secretmanager.secretAccessor
roles/cloudsql.client
roles/storage.objectViewer
```

这样可以保证：

- 应用身份与人员身份分离
- 人员离职不会影响系统运行
- 权限更容易审计
- 符合最小权限原则
- 更适合企业安全治理

---

# 5. Role：可以做什么

Role 表示一组权限。

例如 Cloud Storage 相关权限可能包括：

```text
storage.objects.get
storage.objects.create
storage.objects.delete
storage.buckets.get
```

如果每个权限都单独分配，管理会非常复杂。

因此 GCP 使用 Role 来封装一组权限。

---

# 6. Role 的主要类型

## 6.1 Basic Role

Basic Role 是最基础的角色。

常见包括：

```text
roles/viewer
roles/editor
roles/owner
```

### Viewer

只读权限。

### Editor

读写权限。

### Owner

最高权限，通常包括资源管理、IAM 管理和部分计费相关能力。

生产环境中应尽量避免随意使用 Owner 或 Editor。

---

## 6.2 Predefined Role

Predefined Role 是 GCP 官方预定义的细粒度角色。

企业中最常使用这种角色。

常见示例：

```text
roles/container.admin
roles/cloudsql.admin
roles/cloudsql.client
roles/storage.objectViewer
roles/storage.objectAdmin
roles/secretmanager.secretAccessor
```

例如：

```text
roles/storage.objectViewer
```

表示可以读取 Cloud Storage 对象。

```text
roles/secretmanager.secretAccessor
```

表示可以读取 Secret Manager 中的 Secret 值。

```text
roles/cloudsql.client
```

表示可以连接 Cloud SQL。

---

# 7. 最小权限原则

GCP IAM 的核心安全原则是：

```text
Principle of Least Privilege
```

即：

```text
只授予完成任务所需的最小权限
```

例如：

如果一个服务只需要读取 Secret，就应该授予：

```text
roles/secretmanager.secretAccessor
```

不应该授予：

```text
roles/owner
```

因为 Owner 权限过大，可能导致服务拥有删除资源、修改 IAM、操作数据库等超出业务需求的能力。

---

# 8. Resource：权限作用在哪里

Resource 表示权限作用的目标资源。

常见 Resource 包括：

```text
Project
GKE Cluster
Cloud SQL
Cloud Storage Bucket
Secret Manager Secret
Artifact Registry
```

例如：

```text
analytics-sa
    ↓
roles/storage.objectViewer
    ↓
analytics-data-bucket
```

表示：

```text
analytics-sa 可以读取 analytics-data-bucket
```

---

# 9. Policy：把 Principal、Role、Resource 绑定起来

IAM Policy 是 Principal、Role、Resource 的组合。

结构可以理解为：

```text
Principal
    ↓
Role
    ↓
Resource
```

例如：

```text
order-sa
    ↓
roles/cloudsql.client
    ↓
order-db
```

表示：

```text
order-sa 可以连接 order-db
```

---

# 10. 企业场景示例：应用访问 GCP 服务

假设有一个订单服务：

```text
order-service
```

它需要：

```text
读取订单文件
读取数据库密码
连接数据库
```

推荐设计如下：

```text
order-service
    ↓
order-sa
    ↓
roles/storage.objectViewer
roles/secretmanager.secretAccessor
roles/cloudsql.client
    ↓
Cloud Storage
Secret Manager
Cloud SQL
```

这里需要注意：

```text
order-service
```

是应用名称。

IAM 中真正的 Principal 是：

```text
order-sa@project-id.iam.gserviceaccount.com
```

---

# 11. GKE Pod 访问 GCP 服务的正确理解

面试中经常会问：

```text
GKE Pod 是如何访问 Cloud SQL 或 Cloud Storage 的？
```

不准确的回答：

```text
GKE Cluster 访问 Cloud SQL
Pod 访问 Cloud SQL
```

更准确的回答：

```text
Application
    ↓
Service Account
    ↓
IAM Role
    ↓
GCP Resource
```

也就是说，真正访问资源的是某个 Identity，而不是 GKE 本身。

---

# 12. Terraform Pipeline 应该使用什么身份

如果 Terraform Pipeline 需要自动创建：

```text
GKE
Cloud SQL
Storage Bucket
Artifact Registry
```

不应该使用个人账号：

```text
alice@example.com
```

更合理的方式是使用专门的 Service Account：

```text
terraform-sa@project-id.iam.gserviceaccount.com
```

并根据需要授予它对应权限，例如：

```text
roles/container.admin
roles/cloudsql.admin
roles/storage.admin
roles/artifactregistry.admin
```

在生产环境中，还应该进一步根据实际资源范围进行权限收敛，避免长期使用过大的权限。

---

# 13. Day2 核心图

```text
Principal
    ↓
Role
    ↓
Resource

=

IAM Policy
```

示例：

```text
analytics-sa
    ↓
roles/storage.objectViewer
    ↓
analytics-data-bucket
```

---

# 14. Day2 记忆口诀

```text
Who
Can Do What
On Which Resource
```

对应：

```text
Principal
Role
Resource
```

再进一步：

```text
应用不是身份
Pod 不是身份
Cluster 不是身份
真正访问资源的是 Identity
```

---

# 15. 面试高频问题

## Q1：GCP IAM 主要解决什么问题？

回答：

GCP IAM 用来控制谁可以对哪些资源执行哪些操作。它的核心模型是 Principal、Role、Resource，即 Who、Can Do What、On Which Resource。

---

## Q2：User 和 Service Account 有什么区别？

回答：

User 表示真实人员身份，适合用于人工登录和管理操作。Service Account 表示应用程序、服务或自动化任务的身份，适合用于生产系统、CI/CD Pipeline、GKE Workload、Cloud Run 等场景。

---

## Q3：为什么生产系统不应该使用个人账号运行？

回答：

因为个人账号会受到人员变动影响，不利于长期审计，也不符合最小权限原则。生产系统应该使用专门的 Service Account，并授予最小必要权限。

---

## Q4：什么是最小权限原则？

回答：

最小权限原则指的是只授予完成任务所需的最小权限。例如服务只需要读取 Secret，就只授予 roles/secretmanager.secretAccessor，而不是 roles/owner。

---

## Q5：GKE Pod 如何访问 Cloud SQL？

回答：

更准确的理解是：Pod 中的应用通过绑定的 Service Account 获得 IAM 权限，然后使用对应角色访问 Cloud SQL。访问链路可以理解为 Application → Service Account → IAM Role → Cloud SQL。

---

## Q6：Terraform Pipeline 应该使用什么身份？

回答：

Terraform Pipeline 应该使用专门的 Service Account，例如 terraform-sa，而不是个人用户账号。然后根据需要授予它创建和管理 GKE、Cloud SQL、Storage 等资源的权限。

---

# 16. Day2 总结

Day2 最重要的一句话：

> In GCP, access is always granted to an identity, not to an application name, a VM name, or a Kubernetes cluster name.

翻译：

在 GCP 中，权限永远是授予某个身份，而不是授予应用名称、虚拟机名称或 Kubernetes 集群名称。

真正需要记住的是：

```text
Principal
    ↓
Role
    ↓
Resource
```

以及：

```text
Service Account = 应用和自动化任务的身份
User = 人的身份
Role = 可以做什么
Resource = 权限作用在哪里
Policy = 权限绑定关系
```
