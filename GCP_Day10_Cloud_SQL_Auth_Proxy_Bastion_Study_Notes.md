# GCP 学习路线 - Day 10

# Cloud SQL Auth Proxy 与 Bastion Host：真实企业案例

## 学习目标

- 理解 Cloud SQL Auth Proxy
- 理解 Bastion Host
- 理解 GKE 应用如何访问 Cloud SQL
- 理解开发人员如何通过 DBeaver 访问 Cloud SQL
- 理解 Private IP、IAM、Auth Proxy 的关系

---

## 真实场景：电商支付系统

平台：

shop-platform

微服务：

- web-service
- order-service
- payment-service
- inventory-service

部署平台：

GKE

数据库：

Cloud SQL PostgreSQL

---

## Cloud SQL 实例

实例名称：

prod-payment-db

数据库类型：

PostgreSQL 17

配置示例：

8 vCPU

32GB Memory

Private IP：

10.20.1.5

Region：

asia-southeast1

Project：

shop-prod

---

## Google Service Account

为 payment-service 创建 GSA：

gsa-payment-service@shop-prod.iam.gserviceaccount.com

授予权限：

roles/cloudsql.client

作用：

允许 payment-service 访问 Cloud SQL。

---

## Kubernetes Service Account

GKE 中创建 KSA：

apiVersion: v1
kind: ServiceAccount
metadata:
  name: payment-service-ksa
  namespace: payment-prod

---

## Workload Identity

绑定关系：

payment-service-ksa

↓

Workload Identity

↓

gsa-payment-service@shop-prod.iam.gserviceaccount.com

这样 payment-service Pod 可以使用 GSA 身份访问 GCP 服务。

---

## GKE Deployment 示例

apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
  namespace: payment-prod

spec:
  replicas: 3

  template:
    spec:
      serviceAccountName: payment-service-ksa

      containers:
      - name: payment-service
        image: asia-southeast1-docker.pkg.dev/shop/payment-service:v1

        env:
        - name: DB_HOST
          value: "10.20.1.5"

        - name: DB_NAME
          value: "payment"

        - name: DB_USER
          value: "app_user"

---

## Spring Boot 配置

spring:
  datasource:
    url: jdbc:postgresql://10.20.1.5:5432/payment
    username: app_user
    password: ${DB_PASSWORD}

其中：

DB_PASSWORD

通常来自：

Secret Manager

而不是写死在配置文件中。

---

## 生产环境访问链路

用户付款请求：

User

↓

Load Balancer

↓

Ingress

↓

payment-service

↓

Pod

↓

10.20.1.5

↓

Cloud SQL PostgreSQL

这条链路不经过 Internet。

---

## 实际业务写入示例

用户付款：

Order ID: 100001

Amount: 500 SGD

Status: SUCCESS

SQL 示例：

INSERT INTO payments
(
  order_id,
  amount,
  status
)
VALUES
(
  '100001',
  500,
  'SUCCESS'
);

数据库保存：

order_id    amount    status

100001      500       SUCCESS

---

## 开发人员访问 Cloud SQL 的问题

开发人员使用：

MacBook

工具：

DBeaver

但是 Cloud SQL 只有：

Private IP

开发人员不在 GCP VPC 内，不能直接连接：

10.20.1.5

---

## 方案一：Bastion Host

传统方案：

Laptop

↓

SSH

↓

Bastion Host

↓

Cloud SQL

Bastion Host 本质是一台跳板机。

缺点：

- SSH Key 管理复杂
- 用户账号管理复杂
- 离职权限回收复杂
- 审计复杂
- 运维成本高

---

## 方案二：Cloud SQL Auth Proxy

现代 GCP 推荐方案：

开发人员本地运行：

cloud-sql-proxy shop-prod:asia-southeast1:prod-payment-db

Proxy 在本地启动：

localhost:5432

DBeaver 配置：

Host: localhost

Port: 5432

Database: payment

连接链路：

DBeaver

↓

Cloud SQL Auth Proxy

↓

IAM

↓

Cloud SQL

---

## Cloud SQL Auth Proxy 解决什么问题

Cloud SQL Auth Proxy 主要解决：

- 身份认证
- 加密连接

它不是简单的网络代理。

更准确地说：

Cloud SQL Auth Proxy = IAM 身份认证 + 安全加密连接

---

## 为什么企业喜欢 Cloud SQL Auth Proxy

### IAM 集成

可以通过 IAM 控制谁能访问数据库。

例如：

developers@company.com

授予：

roles/cloudsql.client

即可使用 Proxy 访问。

---

### 权限回收简单

员工离职时：

移除 IAM 权限

访问立即失效。

---

### 审计清晰

Cloud Audit Log 可以记录：

- 谁
- 什么时间
- 连接了哪个 Cloud SQL 实例

---

### 无需开放公网

数据库依然可以保持：

Private IP Only

避免暴露在 Internet。

---

## 生产访问与开发访问的区别

### 生产环境应用访问

payment-service Pod

↓

Private IP

↓

Cloud SQL

特点：

- 性能最好
- 延迟最低
- 网络路径简单
- 适合应用运行时访问

---

### 开发人员访问

DBeaver

↓

Cloud SQL Auth Proxy

↓

IAM

↓

Cloud SQL

特点：

- 不需要进入 VPC
- 使用 IAM 控制权限
- 审计更清晰
- 更适合开发和运维访问

---

## Cloud SQL Auth Proxy Sidecar 模式

有些企业也会让应用 Pod 使用 Cloud SQL Auth Proxy Sidecar。

结构：

Pod

├── App Container

└── Cloud SQL Proxy Container

应用连接：

localhost:5432

实际链路：

App

↓

localhost:5432

↓

Cloud SQL Proxy

↓

IAM

↓

Cloud SQL

适合场景：

- 希望应用通过 IAM 认证连接 Cloud SQL
- 希望减少数据库密码管理
- 对身份审计要求较高

---

## Private IP 直连与 Auth Proxy 对比

### Private IP 直连

Pod

↓

Private IP

↓

Cloud SQL

优点：

- 简单
- 性能好
- 延迟低

适合：

生产应用运行时访问

---

### Cloud SQL Auth Proxy

Client

↓

Proxy

↓

IAM

↓

Cloud SQL

优点：

- IAM 控制
- 加密连接
- 审计清晰
- 不需要直接暴露数据库

适合：

- 开发人员访问
- 运维访问
- 部分高安全应用访问

---

## 面试回答模板

如果面试官问：

GKE 是如何访问 Cloud SQL 的？

可以回答：

在生产环境中，GKE 应用通常通过 Cloud SQL Private IP 直连数据库。
Pod 与 Cloud SQL 位于同一个 VPC 网络体系内，流量不经过 Internet。
数据库用户名和密码通常通过 Secret Manager 管理。

对于开发人员本地访问 Cloud SQL，企业更推荐使用 Cloud SQL Auth Proxy，
因为它可以集成 IAM，提供安全连接，并且避免开放数据库公网访问。

---

## Day10 核心记忆点

Cloud SQL Auth Proxy = IAM 认证 + 加密连接

Bastion Host = 传统跳板机方案

生产应用通常使用 Private IP 直连 Cloud SQL

开发人员通常使用 Cloud SQL Auth Proxy 访问 Cloud SQL

Cloud SQL 不应该随便开放 Public IP

---

## Day10 总结

For production workloads, GKE applications usually access Cloud SQL through Private IP, while developers commonly use Cloud SQL Auth Proxy with IAM for secure local access.

翻译：

生产应用通常通过 Private IP 访问 Cloud SQL，而开发人员通常通过 Cloud SQL Auth Proxy 结合 IAM 实现安全的本地访问。

最终记住两条链路：

生产环境：

Pod

↓

Private IP

↓

Cloud SQL

开发人员：

DBeaver

↓

Cloud SQL Auth Proxy

↓

IAM

↓

Cloud SQL
