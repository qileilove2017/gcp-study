# GCP 学习路线 - Day 6

# Managed Instance Group（MIG）与 Node Pool

## 学习目标

- 理解 Instance Template
- 理解 Managed Instance Group（MIG）
- 理解 Auto Healing
- 理解 Auto Scaling
- 理解 Node Pool
- 理解 Cluster Autoscaler
- 理解 GKE 与 MIG 的关系

---

## 为什么需要 MIG

如果只有单台 VM：

VM1

运行业务系统

当 VM1 宕机时：

运维需要手工创建新 VM 并恢复服务。

企业生产环境通常无法接受这种恢复方式。

因此需要：

Managed Instance Group（MIG）

---

## 什么是 MIG

MIG 可以理解为：

VM 管理器

例如：

Desired = 3

表示：

始终保持 3 台 VM。

如果：

VM2 挂掉

MIG 发现：

Current = 2

Desired = 3

自动创建：

VM4

恢复容量。

---

## Instance Template

MIG 创建 VM 时需要模板。

例如：

Machine Type:

e2-standard-4

CPU:

4

Memory:

16GB

OS:

Ubuntu

Startup Script:

Install Java

MIG 根据模板自动创建 VM。

---

## Auto Healing

Health Check 失败时：

MIG 自动：

删除故障 VM

创建新 VM

实现自动修复。

---

## Auto Scaling

流量增加：

CPU 使用率升高。

MIG 自动扩容：

3 VM

↓

10 VM

流量下降后：

10 VM

↓

3 VM

实现自动伸缩。

---

## Node Pool

很多人认为：

Node Pool = Node 集合

更准确的理解：

Node Pool = 同配置 VM 集合

例如：

Node Pool A

e2-standard-4

4 CPU

16GB

Node Pool B

e2-standard-16

16 CPU

64GB

---

## Node Pool 与 MIG

核心关系：

Node Pool

↓

MIG

↓

VM

↓

Node

例如：

Node Pool

3 Nodes

背后实际上：

MIG

Desired = 3

创建：

VM1

VM2

VM3

然后注册：

Node1

Node2

Node3

---

## Cluster Autoscaler

发现：

Pod Pending

原因：

Node 资源不足

Cluster Autoscaler：

修改：

MIG Desired

3

↓

5

MIG 创建：

VM4

VM5

GKE 注册：

Node4

Node5

Pod 被调度进去。

---

## Node 宕机

Node2 宕机：

实际上：

VM2 宕机

Kubernetes 只能发现：

Node NotReady

但无法创建 VM。

负责恢复的是：

MIG

自动创建新 VM

并注册为新 Node。

---

## 排查思路

很多问题表面是：

Pod 问题

实际上可能是：

- Node Pool 配置问题
- MIG 上限问题
- Compute Engine 配额问题

排查链路：

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

## 核心架构图

Cluster Autoscaler

↓

Node Pool

↓

Managed Instance Group

↓

Compute Engine VM

↓

Kubernetes Node

↓

Pod

---

## 面试高频题

Q1

什么是 MIG？

GCP 的 VM 生命周期管理服务。

---

Q2

Node Pool 和 MIG 的关系？

Node Pool 底层依赖 MIG。

---

Q3

Node 宕机谁负责恢复？

MIG。

---

Q4

Cluster Autoscaler 是否直接创建 Node？

不是。

它修改 MIG Desired Count。

---

Q5

Instance Template 的作用？

定义 VM 标准配置。

---

## Day6 核心记忆点

Instance Template = VM 模板

MIG = VM 管理器

Node Pool = MIG 的 GKE 封装

Cluster Autoscaler → 修改 MIG

MIG → 创建 VM

GKE → 注册 Node

---

## Day6 总结

Pod

↓

Node

↓

Node Pool

↓

Managed Instance Group

↓

Compute Engine VM

↓

Google Datacenter

Node Pool 并不直接管理 Node，而是依赖 MIG 创建和维护底层 VM。
