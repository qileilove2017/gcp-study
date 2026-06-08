# GCP 学习路线 - Day 11

# Cloud DNS 深度解析

## 学习目标

- 理解 DNS
- 理解 Cloud DNS
- 理解 DNS Zone
- 理解 DNS Record
- 理解 A Record
- 理解 TTL
- 理解 DNS 与 Ingress 的区别
- 理解企业迁移中的 DNS

---

## 为什么需要 DNS

假设公司系统：

电商系统：

34.120.88.10

支付系统：

35.220.10.5

管理后台：

34.155.99.8

如果没有 DNS：

用户只能记忆 IP。

因此需要：

DNS

---

## DNS 本质

DNS：

Domain Name System

可以理解为：

互联网电话簿

示例：

Lei

↓

+65 9123 xxxx

DNS：

shop.company.com

↓

34.120.88.10

作用：

域名转换为 IP。

---

## 浏览器访问网站过程

访问：

https://shop.company.com

实际流程：

Browser

↓

DNS Query

↓

shop.company.com

↓

34.120.88.10

↓

TCP

↓

HTTPS

↓

Load Balancer

浏览器最终访问的是 IP。

---

## Cloud DNS

Cloud DNS：

Google Cloud 托管 DNS 服务。

无需自行维护：

- Bind
- Windows DNS

Cloud DNS = Google 帮你运维 DNS

---

## DNS Zone

公司拥有：

company.com

创建：

Managed Zone

名称：

company-com

域名：

company.com

管理：

- shop.company.com
- api.company.com
- admin.company.com
- grafana.company.com

---

## DNS Record

Zone 中存放：

Record

最常见：

A Record

作用：

域名

↓

IPv4

例如：

shop.company.com

↓

34.120.88.10

---

## 企业真实案例

GKE 创建：

Ingress

↓

Load Balancer

IP：

34.120.88.10

Cloud DNS 配置：

A Record

shop.company.com

↓

34.120.88.10

访问链路：

User

↓

Cloud DNS

↓

34.120.88.10

↓

Load Balancer

↓

Ingress

↓

Pod

---

## DNS 与 Ingress 的区别

DNS：

域名

↓

IP

例如：

api.company.com

↓

34.120.88.10

---

Ingress：

请求

↓

Service

例如：

/api/*

↓

api-service

---

/admin/*

↓

admin-service

---

记忆口诀：

DNS 找大楼

Ingress 找房间

---

## DNS 解耦

今天：

shop.company.com

↓

34.120.88.10

明天：

shop.company.com

↓

35.201.50.20

用户网址不变。

只需修改：

DNS Record

---

## 企业迁移案例

旧系统：

AWS

↓

44.200.x.x

新系统：

GCP

↓

34.120.x.x

用户继续访问：

shop.company.com

只需修改：

A Record

---

## TTL

TTL：

Time To Live

作用：

DNS 缓存时间

例如：

TTL = 300

缓存：

5分钟

---

TTL = 86400

缓存：

24小时

---

## TTL 对迁移影响

原记录：

shop.company.com

↓

34.120.88.10

修改：

shop.company.com

↓

35.201.50.20

如果 TTL=86400

部分用户可能继续访问旧 IP 长达 24 小时。

---

## 企业迁移最佳实践

平时：

TTL = 86400

迁移前：

TTL = 300

等待：

24小时

再切换 IP。

最终约 5 分钟完成全网切换。

---

## 企业典型架构

shop.company.com

↓

Cloud DNS

↓

34.120.88.10

↓

Load Balancer

↓

Ingress

↓

shop-service

---

api.company.com

↓

Cloud DNS

↓

34.120.88.10

↓

Load Balancer

↓

Ingress

↓

api-service

---

## Cloud DNS 在 GCP 中的位置

Browser

↓

Cloud DNS

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

## 面试高频题

Q1

什么是 DNS？

域名到 IP 地址映射系统。

---

Q2

Cloud DNS 是什么？

Google Cloud 托管 DNS 服务。

---

Q3

什么是 Zone？

域名管理空间。

---

Q4

什么是 A Record？

域名到 IPv4 的映射。

---

Q5

DNS 与 Ingress 区别？

DNS：

域名 → IP

Ingress：

请求 → Service

---

Q6

TTL 是什么？

DNS 缓存时间。

---

Q7

为什么迁移前降低 TTL？

加快 DNS 切换速度。

---

## Day11 核心记忆点

DNS = 域名 → IP

Cloud DNS = Google 托管 DNS

Zone = 域名管理空间

A Record = 域名 → IPv4

TTL = DNS 缓存时间

DNS 找大楼

Ingress 找房间

---

## Day11 总结

Cloud DNS translates domain names into IP addresses and acts as the first entry point of application traffic.

Cloud DNS 将域名解析为 IP 地址，是应用访问链路的第一站。

最终链路：

Browser

↓

Cloud DNS

↓

Load Balancer

↓

Ingress

↓

Service

↓

Pod
