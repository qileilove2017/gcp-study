# GCP 学习路线 - Day 9

# Cloud SQL（GCP 托管数据库）

## 学习目标

- 理解 Cloud SQL
- 理解 Cloud SQL 与 PostgreSQL 的关系
- 理解托管数据库与自建数据库区别
- 理解 Private IP
- 理解 GKE 如何访问 Cloud SQL
- 理解为什么数据库通常不开放公网

---

## 什么是 Cloud SQL

Cloud SQL 不是一种新的数据库。

Cloud SQL = Google 帮你运维的数据库。

支持：

- PostgreSQL
- MySQL
- SQL Server

企业最常见：

PostgreSQL

---

## 自建 PostgreSQL

传统模式：

Compute Engine

↓

Ubuntu

↓

PostgreSQL

需要自己负责：

- 安装
- 备份
- 升级
- 高可用
- 故障恢复

---

## Cloud SQL

Cloud SQL 模式：

Create Instance

↓

Google 自动安装数据库

↓

直接使用

Google 负责：

- 操作系统
- 数据库安装
- 补丁升级
- 自动备份
- 故障恢复
- 监控

---

## Cloud SQL 本质

Cloud SQL

=

托管版 PostgreSQL

类似：

AWS RDS

Azure Database for PostgreSQL

---

## Cloud SQL 适合存什么

Cloud SQL：

- 用户
- 订单
- 支付记录
- 库存
- 产品信息

Cloud Storage：

- 图片
- PDF
- 视频
- Excel

---

## 企业真实架构

GKE Pod

↓

Cloud SQL

例如：

payment-service

↓

payment-db

---

## 网络架构

企业生产环境：

通常使用 Private IP

例如：

Cloud SQL

10.30.0.20

---

## Pod 为什么能访问数据库

原因：

同一个 VPC。

示例：

VPC

├── GKE Subnet

│   ├── Node

│   └── Pod

│

└── Cloud SQL

    └── 10.30.0.20

---

访问路径：

payment-pod

↓

10.30.0.20

↓

Cloud SQL

---

整个过程：

不经过 Internet

---

## Private IP 的优势

更安全

数据库没有公网地址

---

更快

走 Google 内网

---

更便宜

内网流量成本更低

---

## 数据库认证

即使网络打通：

Pod

↓

Cloud SQL

仍需要：

- 用户名
- 密码

例如：

app_user

********

---

企业通常：

Secret Manager

管理密码

---

## 企业完整链路

User

↓

Load Balancer

↓

Ingress

↓

Service

↓

Pod

↓

Private IP

↓

Cloud SQL

---

## 故障排查思路

Pod 无法访问数据库：

检查：

1. Cloud SQL 状态
2. Private IP
3. VPC 网络
4. 用户名密码
5. Secret 配置

---

## 面试高频题

Q1

什么是 Cloud SQL？

Google 托管数据库服务。

---

Q2

Cloud SQL 和 PostgreSQL 的关系？

Cloud SQL 是托管版 PostgreSQL。

---

Q3

为什么企业喜欢 Cloud SQL？

无需自己维护数据库。

---

Q4

为什么 Cloud SQL 通常使用 Private IP？

更安全、更快、更便宜。

---

Q5

Pod 为什么能访问 Cloud SQL？

因为位于同一个 VPC 网络体系。

---

## Day9 核心记忆点

Cloud SQL

=

Google 帮你运维的 PostgreSQL

---

Cloud Storage

存文件

---

Cloud SQL

存结构化数据

---

Private IP

=

数据库标准方案

---

Pod

↓

Private IP

↓

Cloud SQL

---

## Day9 总结

Cloud SQL is a managed PostgreSQL service that typically uses Private IP to securely serve applications running inside the same VPC.

Cloud SQL 是 Google 托管的 PostgreSQL 服务，通常通过 Private IP 为同一 VPC 内的应用提供安全访问。
