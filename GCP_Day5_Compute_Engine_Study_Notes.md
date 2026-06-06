# GCP 学习路线 - Day 5

# Compute Engine（GCP 计算基础）

## 学习目标

- 理解 Compute Engine
- 理解 VM、Node、Pod 的关系
- 理解 Request 和 Limit
- 理解 Kubernetes 调度
- 理解 Cluster Autoscaler

---

## 什么是 Compute Engine

Compute Engine 是 GCP 的虚拟机服务。

类似：

- AWS EC2
- Azure VM

本质提供：

- CPU
- Memory
- Disk
- Network

---

## Compute Engine 的本质

Google Datacenter

↓

Compute Engine VM

↓

业务系统

Compute Engine 可以理解为云上的服务器。

---

## GKE 与 Compute Engine 的关系

很多人认为：

GKE

↓

Pod

实际上：

GKE

↓

Node Pool

↓

Node

↓

Compute Engine VM

↓

Pod

---

## Node 是什么

Node 本质上就是：

Compute Engine VM

例如：

e2-standard-4

4 vCPU

16GB Memory

---

## Pod 与 Node

Pod 运行在 Node 上。

Node 提供资源：

- CPU
- Memory
- Disk

Pod 消耗这些资源。

---

## 一个 VM 可以运行多个 Pod

正确理解：

1 VM ≠ 1 Pod

而是：

1 VM = N 个 Pod

Node 是资源池。

Pod 是资源消费者。

---

## CPU 与 Memory

CPU：

可以共享。

多个 Pod 竞争 CPU 时间片。

Memory：

不能无限共享。

总内存使用不能超过 Node 提供的内存。

---

## Requests

示例：

requests:

cpu: 2

memory: 4Gi

含义：

调度时至少需要：

- 2 CPU
- 4GB Memory

---

## Scheduler 看什么

Scheduler 不看：

实际使用率

Scheduler 看：

Request

---

示例

Node：

8 CPU

32GB

Pod A：

4 CPU

16GB

Pod B：

4 CPU

16GB

虽然实际使用率只有 10%，

新的 Pod 仍然无法调度。

因为 Request 已经占满资源。

---

## 为什么这样设计

为了保证资源承诺。

避免高峰期资源不足导致整个 Node 崩溃。

---

## Cluster Autoscaler

当 Pod 无法调度：

Pending

原因：

Node 资源不足。

Cluster Autoscaler：

新增 Node

↓

新增 Compute Engine VM

---

## Machine Type

常见规格：

e2-micro

e2-standard-2

2 CPU

8GB

e2-standard-4

4 CPU

16GB

e2-standard-8

8 CPU

32GB

---

## 常见问题

Pod Pending

可能：

Node CPU 不足

OOMKilled

可能：

Node Memory 不足

ImagePullBackOff

可能：

Node 网络问题

DiskPressure

可能：

Node 磁盘不足

---

## 核心链路

Google Datacenter

↓

Compute Engine

↓

Node

↓

Pod

---

## 面试高频题

Q1

Compute Engine 是什么？

GCP 的虚拟机服务。

---

Q2

Node 本质是什么？

Compute Engine VM。

---

Q3

Pod 是否直接运行在 GCP 上？

不是。

运行在 Node 上。

---

Q4

Scheduler 看什么？

Request。

---

Q5

为什么会触发 Cluster Autoscaler？

Node 资源不足导致 Pod Pending。

---

## Day5 核心记忆点

Compute Engine = GCP VM

Node = VM

Pod 运行在 Node 上

Node = 资源池

Pod = 资源消费者

Scheduler 看 Request

Pod 不够资源

↓

Node 不够

↓

新增 VM

---

## Day5 总结

Pod 不直接消耗云资源。

Pod 消耗 Node 的资源。

Node 消耗 Compute Engine 的资源。

最终关系：

Compute Engine

↓

Node

↓

Pod
