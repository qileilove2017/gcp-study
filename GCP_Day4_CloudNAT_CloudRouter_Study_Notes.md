# GCP 学习路线 - Day 4

# Cloud NAT、Cloud Router 与 Private Subnet

## 学习目标

- 理解 Private Subnet
- 理解 Cloud NAT
- 理解 Cloud Router
- 理解 Private GKE 如何访问 Internet
- 理解 NAT Port Exhaustion

---

## Private Subnet

Private Subnet 本质：

没有公网 IP 的子网。

例如：

- 10.0.1.0/24

部署：

- GKE Node
- Cloud SQL
- Redis

资源只能使用私网 IP 通信。

---

## 为什么不能直接访问 Internet

私网地址：

- 10.x.x.x
- 172.16.x.x
- 192.168.x.x

不会被 Internet 路由。

因此：

10.0.1.10

无法直接访问：

- Docker Hub
- GitHub
- Maven
- NPM

---

## Cloud NAT

Cloud NAT：

Cloud Network Address Translation

作用：

将私网地址转换为公网地址。

示例：

10.0.1.10

↓

34.100.1.1

↓

Internet

---

## Cloud NAT 的特点

支持：

内部 → 外部

不支持：

外部 → 内部

因此：

Cloud NAT 不能让 Internet 主动访问私有资源。

---

## 为什么企业喜欢 NAT

方案1：

所有 Node 都有公网IP

方案2：

所有 Node 使用私网IP

共享 Cloud NAT

企业一般选择方案2：

- 更安全
- 降低攻击面
- 节省公网IP

---

## Cloud Router

Cloud Router 不是流量转发器。

它更像：

网络导航系统。

负责：

- Route 管理
- 路由控制
- BGP 路由交换

---

## Cloud Router 与 NAT 的关系

Cloud NAT 创建时需要绑定 Cloud Router。

Cloud Router 负责告诉 GCP：

哪些网段通过 NAT 出网。

例如：

10.0.1.0/24

↓

Cloud NAT

---

## Control Plane 与 Data Plane

Cloud Router：

Control Plane

负责：

- Route
- 配置

Cloud NAT：

Data Plane

负责：

- 地址转换
- 实际流量

---

## GKE 场景

Private GKE：

Node1 10.0.1.10

Node2 10.0.1.11

Pod 启动时：

docker pull nginx

访问：

- Docker Hub
- Artifact Registry

路径：

Pod

↓

Node

↓

Cloud NAT

↓

Internet

---

## NAT Port Exhaustion

大量 Pod 共享 NAT 时：

可能出现：

- Connection Timeout
- Connection Reset
- Random Failure

原因：

NAT Port Exhaustion

解决：

增加 NAT 公网 IP。

---

## 面试高频题

Q1

为什么 Private GKE 可以访问 Internet？

A：

Cloud NAT 提供出站访问能力。

---

Q2

Cloud NAT 是否允许 Internet 主动访问内部资源？

A：

不允许。

---

Q3

Cloud Router 是否转发业务流量？

A：

不转发。

负责路由控制。

---

Q4

Cloud NAT 和 Load Balancer 区别？

Cloud NAT：

内部 → 外部

Load Balancer：

外部 → 内部

---

## 核心记忆点

Private Subnet = 没有公网IP

Cloud NAT = 私网出网

Cloud Router = 路由控制器

Cloud Router = Control Plane

Cloud NAT = Data Plane

---

## Day4 总结

Private Subnet
↓
Cloud NAT
↓
Internet

Cloud NAT 负责让私有资源访问互联网。

Cloud Router 负责管理这些路由配置。
