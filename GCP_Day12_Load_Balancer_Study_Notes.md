# GCP 学习路线 - Day 12

# Cloud Load Balancer 深度解析

## 核心内容

- Load Balancer = 流量分发
- Health Check = 自动摘除故障实例
- Global Load Balancer = 全球流量调度
- Anycast = 一个 IP 多地存在
- Multi-Region = 容灾高可用

---

## 流量链路

Browser
↓
Cloud DNS
↓
Global Load Balancer
↓
Ingress
↓
Service
↓
Pod
↓
Cloud SQL

---

## 为什么需要 Load Balancer

当 payment-service 有多个 Pod 时，如果所有流量进入一个 Pod，会导致资源耗尽，而其他 Pod 空闲。

Load Balancer 的作用就是把流量分发到多个后端实例。

---

## Health Check

Health Check 会持续检测 Pod 是否健康。

- 健康：继续接收流量
- 不健康：自动摘除

Kubernetes 常见三层检查：

- Liveness Probe（是否活着）
- Readiness Probe（是否准备接流量）
- Load Balancer Health Check（后端是否可服务）

---

## Global Load Balancer

部署区域：

- Singapore
- Tokyo
- Sydney

Google 会根据：

1. 健康状态
2. 网络延迟
3. 地理位置
4. 后端负载

自动选择最佳 Region。

---

## Anycast

同一个公网 IP 可以同时存在于多个 Google Region。

用户访问同一个域名时，Google 全球网络会决定进入哪个 Region。

---

## 容灾架构

Single Region：

Singapore 故障 -> 全站不可用

Multi Region：

Singapore 故障 -> Tokyo/Sydney 自动接管

---

## 面试高频题

Q1 什么是 Load Balancer？

流量分发组件。

Q2 为什么需要 Health Check？

自动摘除故障实例。

Q3 Global Load Balancer 的价值？

全球流量调度与容灾。

Q4 DNS 和 Load Balancer 区别？

DNS：域名 -> IP

Load Balancer：流量 -> 后端服务

---

## Day12 总结

Global Load Balancer 不仅是负载均衡器，更是全球流量调度与容灾平台。