# GCP 学习路线 - Day 7

# 企业级 GKE 流量链路与 Week1 总复习

## 学习目标

- 串联前6天知识
- 理解用户请求如何进入 GKE
- 理解 Load Balancer、Ingress、Service
- 理解 Pod 如何访问数据库
- 理解 Pod 如何访问 Internet
- 建立完整 GCP 架构脑图

---

## 用户请求完整链路

用户访问：

https://payment.company.com

完整路径：

User

↓

Internet

↓

Load Balancer

↓

Ingress

↓

Service

↓

Pod

↓

Cloud SQL

---

## Load Balancer

作用：

- 公网入口
- 接收 Internet 流量
- 转发到 GKE

为什么需要：

Pod IP 通常是：

10.x.x.x

属于私网地址。

Internet 无法直接访问。

理解：

Load Balancer = 系统大门

---

## Ingress

作用：

路由规则管理。

例如：

/api/*

↓

api-service

/web/*

↓

web-service

理解：

Ingress = 前台接待

---

## Service

作用：

为 Pod 提供稳定访问入口。

例如：

payment-service

↓

payment-pod-1

payment-pod-2

payment-pod-3

即使 Pod 重建，Service 地址保持不变。

理解：

Service = VIP

---

## Pod 宕机

假设：

payment-pod-2

挂掉。

Kubernetes 自动移除 Endpoint。

流量继续转发：

payment-pod-1

payment-pod-3

用户通常无感知。

---

## Deployment 为什么 3 副本

replicas: 3

原因：

Pod1 挂了

还有 Pod2

还有 Pod3

实现高可用。

---

## Pod 如何访问 Cloud SQL

Cloud SQL 通常部署在：

Data Subnet

通过：

Private IP

通信。

路径：

Pod

↓

Cloud SQL

---

## Pod 如何访问 Internet

例如访问：

- GitHub
- Docker Hub
- OpenAI API

路径：

Pod

↓

Node

↓

Cloud NAT

↓

Internet

---

## Week1 架构总图

基础设施：

Project

↓

IAM

↓

VPC

↓

Subnet

↓

Cloud NAT

↓

Compute Engine

↓

Managed Instance Group

↓

Node Pool

↓

Node

↓

Pod

---

业务流量：

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

Cloud SQL

---

## 各组件职责

Load Balancer

解决：

Internet 如何访问系统

职责：

公网入口

---

Ingress

解决：

流量去哪个服务

职责：

路由规则

---

Service

解决：

Pod IP 不稳定

职责：

稳定访问入口

---

Pod

职责：

运行应用代码

---

Cloud SQL

职责：

数据存储

---

## 故障排查思路

用户无法访问：

Load Balancer

↓

Ingress

↓

Service

↓

Pod

↓

Database

---

Pod Pending：

Pod

↓

Node

↓

Node Pool

↓

MIG

↓

Compute Engine

---

## 面试高频题

Q1

为什么用户不能直接访问 Pod？

因为 Pod 使用私网 IP。

---

Q2

Load Balancer 的作用？

公网入口。

---

Q3

Ingress 的作用？

流量路由。

---

Q4

Service 的作用？

稳定入口和负载均衡。

---

Q5

Pod 挂了用户一定受影响吗？

不一定。

Service 会自动切换到健康 Pod。

---

## Day7 核心记忆点

Load Balancer = 公网入口

Ingress = 路由规则

Service = 稳定入口

Pod = 业务代码

Cloud NAT = 私网出网

---

## 核心链路

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

Cloud SQL

---

## Week1 总结

已经掌握：

- Project
- IAM
- VPC
- Subnet
- Cloud NAT
- Compute Engine
- MIG
- Node Pool
- Node
- Pod
- Load Balancer
- Ingress
- Service

具备进入 Week2（Cloud Storage、Cloud SQL、Cloud DNS、Cloud Run）的基础。
