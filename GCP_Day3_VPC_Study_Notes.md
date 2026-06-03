# GCP 学习路线 - Day 3

## VPC（Virtual Private Cloud）与企业网络架构

Day3 核心内容：

- VPC 是什么
- 为什么企业需要 VPC
- 什么是 Subnet
- 为什么需要网络分层
- Firewall 的作用
- GCP 与 AWS 网络架构差异
- GKE 与 VPC 的关系

---

## 核心概念

### VPC

VPC（Virtual Private Cloud）可以理解为：

企业在云平台上的私有网络。

例如：

VPC: 10.0.0.0/16

内部部署：

- Web Service
- API Service
- PostgreSQL
- Redis
- GKE

资源之间通过私有 IP 通信，而不是经过 Internet。

---

### 私网地址

常见私网地址：

- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

这些地址无法被 Internet 直接访问。

---

### VPC 与 Azure 对比

Azure VNet ≈ GCP VPC

二者都用于构建企业私有网络。

---

## Subnet

Subnet（子网）用于把一个大网络拆分成多个小网络。

例如：

VPC: 10.0.0.0/16

拆分：

- 10.0.1.0/24
- 10.0.2.0/24
- 10.0.3.0/24

---

## 为什么需要 Subnet

核心原因：

安全隔离。

企业通常会划分：

- Web Subnet
- App Subnet
- Data Subnet

示例：

Web Layer
↓
Application Layer
↓
Database Layer

这样即使 Web 层被攻击，也无法直接访问数据库。

---

## Network Segmentation

网络分层隔离。

目的：

- 降低攻击面
- 限制横向移动
- 保护核心数据

---

## Firewall

Firewall 可以理解为网络门禁。

决定：

谁可以访问谁。

例如：

允许：

- Web → API
- API → PostgreSQL

禁止：

- Web → PostgreSQL

---

## GCP 与 AWS 最大区别

AWS：

VPC = Regional

GCP：

VPC = Global

---

### GCP 架构

Global VPC

- Singapore Subnet
- Tokyo Subnet
- Sydney Subnet

牢记：

VPC = Global

Subnet = Regional

---

## GKE 与网络

GKE Pod 本质上拥有自己的 IP。

例如：

- frontend-pod
- payment-pod
- order-pod

服务调用本质上是网络通信。

---

## 企业级架构

Global VPC

- web-subnet
- app-subnet
- data-subnet

部署：

web-subnet：
- Frontend
- Load Balancer

app-subnet：
- GKE
- API Service

data-subnet：
- Cloud SQL
- Redis

访问路径：

Internet
→ Frontend
→ API
→ Database

---

## 面试高频题

Q1：什么是 VPC？

A：云上的私有网络。

Q2：为什么数据库不暴露公网？

A：提高安全性，降低攻击面。

Q3：为什么需要 Subnet？

A：网络分层和隔离。

Q4：什么是 Network Segmentation？

A：通过 Subnet 和 Firewall 实现安全隔离。

Q5：GCP 与 AWS VPC 最大区别？

A：

AWS：VPC = Regional

GCP：VPC = Global

---

## Day3 核心记忆点

1. VPC = 企业私有网络
2. Subnet = 网络分区
3. Firewall = 网络门禁
4. Network Segmentation = 网络分层隔离
5. GCP：VPC = Global，Subnet = Regional

---

## Day3 总结

VPC 提供私有网络。

Subnet 提供网络分层。

Firewall 提供访问控制。

三者共同构成企业级云网络架构基础。
