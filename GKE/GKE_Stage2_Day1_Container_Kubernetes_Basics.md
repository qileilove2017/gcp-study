# GKE 学习笔记：第二阶段 Day 1

## Day 1 主题

**Container 到底解决什么问题？**

本节目标不是深入学习 Docker 或 Kubernetes 的所有细节，而是先建立后续学习 GKE 必须掌握的核心主线：

```text
代码
→ Docker Image
→ Container
→ Pod
→ Deployment
→ Service
→ Ingress / Load Balancer
→ GKE
```

---

## 1. 为什么需要 Container？

在没有容器之前，一个应用从开发环境部署到服务器，经常会遇到以下问题：

```text
Java / Node / Python 版本不一致
环境变量不一致
依赖库缺失
配置文件路径不同
操作系统差异
网络端口冲突
启动脚本不一致
```

典型问题是：

```text
开发说：我本地是好的。
运维说：服务器上跑不起来。
测试说：环境又坏了。
```

Container 要解决的核心问题是：

```text
把应用和它运行所需的环境一起打包。
```

也就是说，容器让应用在不同环境中尽可能保持一致的运行方式。

---

## 2. Image 和 Container 的区别

可以这样理解：

```text
Image = 应用的安装包 / 模板
Container = Image 运行起来之后的实例
```

例如：

```text
nginx image
```

只是一个镜像文件。

运行起来之后：

```text
nginx container
```

才是真正正在运行的服务。

类比理解：

```text
Docker Image ≈ Java class
Container ≈ Java object instance
```

或者：

```text
Docker Image ≈ 软件安装包
Container ≈ 正在运行的软件进程
```

---

## 3. Dockerfile 是什么？

Dockerfile 是用来定义镜像如何构建的文件。

一个简单的 Node.js Dockerfile 示例：

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

它表达的含义是：

```text
使用 node:20-alpine 作为基础环境
进入 /app 目录
复制依赖文件
安装依赖
复制代码
暴露 3000 端口
启动 node server.js
```

完整流程是：

```text
源码 + Dockerfile
        ↓
docker build
        ↓
Docker Image
        ↓
docker run
        ↓
Container
```

---

## 4. 为什么学习 GKE 必须先理解 Image？

部署到 GKE 的不是源代码，而是：

```text
Container Image
```

例如：

```text
asia-southeast1-docker.pkg.dev/my-project/my-repo/order-service:v1
```

GKE / Kubernetes 会根据 YAML 文件去拉取这个镜像，并运行成 Pod。

因此后续 GKE 部署主线是：

```text
代码写完
→ 打包成镜像
→ 推送到 Artifact Registry
→ GKE 从 Artifact Registry 拉镜像
→ 创建 Pod
→ 对外暴露服务
```

---

## 5. 为什么有了 Docker，还需要 Kubernetes？

Docker 可以运行单个容器：

```bash
docker run -p 8080:8080 my-app:v1
```

但是生产环境通常不是只运行一个容器，而是要考虑：

```text
服务挂了怎么办？
流量大了怎么扩容？
新版本怎么发布？
多个副本怎么负载均衡？
机器坏了怎么办？
配置怎么管理？
日志怎么看？
服务之间怎么通信？
```

Docker 主要解决：

```text
怎么把应用打包并运行起来
```

Kubernetes 解决：

```text
怎么在一组机器上管理大量容器
```

所以可以这样理解：

```text
Docker 管一个 Container
Kubernetes 管一堆 Container
GKE 帮你托管 Kubernetes
```

---

## 6. Kubernetes 必须先掌握的 6 个核心概念

本阶段先掌握以下 6 个概念即可：

```text
Cluster
Node
Pod
Deployment
Service
Namespace
```

---

## 6.1 Cluster

Cluster 是 Kubernetes 集群。

可以理解为：

```text
一组专门用来运行容器应用的机器 + 一套管理系统
```

在 GKE 中，一个 GKE Cluster 就是一个 Kubernetes Cluster。

---

## 6.2 Node

Node 是集群里的工作机器。

简单理解：

```text
Node = 真正运行 Pod 的机器
```

在 GKE Standard 模式下，你可以比较明显地看到 Node Pool 和 Node。

在 GKE Autopilot 模式下，Google 会帮你弱化很多节点管理细节。

---

## 6.3 Pod

Pod 是 Kubernetes 里最重要的概念。

可以先这样记：

```text
Pod = Kubernetes 里运行应用的最小单元
```

一个 Pod 里面通常运行一个主容器，例如：

```text
order-service container
payment-service container
user-service container
```

需要注意：

```text
Pod 可以被创建
Pod 可以被删除
Pod 可以被重建
Pod 的 IP 会变
Pod 不应该被直接依赖
```

这点非常重要。

---

## 6.4 Deployment

Deployment 是用来管理 Pod 的。

例如你希望系统永远运行 3 个 order-service：

```yaml
replicas: 3
```

Deployment 会负责保证：

```text
现在少了一个 Pod？
那我再创建一个。

现在多了一个 Pod？
那我删掉一个。

现在镜像版本变了？
那我滚动更新。

新版本有问题？
那我可以回滚。
```

所以：

```text
Pod 是实际运行单元
Deployment 是管理 Pod 的控制器
```

---

## 6.5 Service

Pod 的 IP 会变，所以不能让其他服务直接访问 Pod IP。

例如：

```text
payment-service 不能直接访问 order-service 的 Pod IP
```

因为 Pod 重启之后，IP 可能变了。

Service 的作用是：

```text
给一组 Pod 提供一个稳定访问入口
```

例如：

```text
order-service.default.svc.cluster.local
```

这样 payment-service 调用 order-service 时，不用关心后面具体是哪一个 Pod。

核心结论：

```text
Pod 会变
Service 稳定
```

---

## 6.6 Namespace

Namespace 用来做逻辑隔离。

例如：

```text
dev namespace
sit namespace
uat namespace
prod namespace
```

或者：

```text
team-a namespace
team-b namespace
```

可以理解为：

```text
Namespace = Kubernetes 里的逻辑空间
```

---

## 7. 最小 Kubernetes YAML 示例

### 7.1 Deployment 示例

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
        - name: order-service
          image: nginx:latest
          ports:
            - containerPort: 80
```

它的意思是：

```text
我要创建一个 Deployment
名字叫 order-service
希望运行 2 个副本
Pod 的标签是 app=order-service
容器镜像使用 nginx:latest
容器监听 80 端口
```

---

### 7.2 Service 示例

```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

它的意思是：

```text
创建一个稳定入口，名字叫 order-service
把流量转发给 app=order-service 的 Pod
Service 端口是 80
Pod 容器端口也是 80
这个服务只在集群内部访问
```

---

## 8. Deployment 和 Service 的关系

关系图：

```text
Deployment
   ↓ 创建和管理
Pod 1   Pod 2   Pod 3
   ↑      ↑      ↑
   └──── Service ──── 稳定访问入口
```

Deployment 关心的是：

```text
我要运行几个 Pod？
用哪个镜像？
怎么更新？
```

Service 关心的是：

```text
怎么访问这些 Pod？
怎么做负载均衡？
怎么提供稳定地址？
```

---

## 9. 用真实业务理解 Kubernetes

假设有 3 个服务：

```text
frontend
order-service
payment-service
```

在 Kubernetes 中大概是这样：

```text
frontend Deployment
frontend Service

order-service Deployment
order-service Service

payment-service Deployment
payment-service Service
```

服务调用关系：

```text
frontend → order-service Service → order-service Pods
order-service → payment-service Service → payment-service Pods
```

代码里通常不是调用某个 Pod IP，而是调用 Service 名称：

```text
http://order-service/api/orders
http://payment-service/api/payments
```

在同一个 Namespace 下，可以直接使用 Service name。

---

## 10. kubectl 常用命令

kubectl 是 Kubernetes 的命令行工具，用于部署、查看和管理 Kubernetes 资源。

### 10.1 查看资源

```bash
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get namespaces
```

### 10.2 查看详细信息

```bash
kubectl describe pod <pod-name>
kubectl describe deployment <deployment-name>
kubectl describe service <service-name>
```

### 10.3 查看日志

```bash
kubectl logs <pod-name>
```

### 10.4 进入容器

```bash
kubectl exec -it <pod-name> -- sh
```

### 10.5 应用 YAML

```bash
kubectl apply -f app.yaml
```

### 10.6 删除 YAML 创建的资源

```bash
kubectl delete -f app.yaml
```

### 10.7 查看滚动发布状态

```bash
kubectl rollout status deployment/order-service
```

### 10.8 回滚 Deployment

```bash
kubectl rollout undo deployment/order-service
```

---

## 11. 本节必须记住的 5 句话

```text
1. Image 是模板，Container 是运行实例。
2. Pod 是 Kubernetes 里运行应用的最小单元。
3. Deployment 负责管理 Pod 的副本和发布。
4. Service 负责给 Pod 提供稳定访问入口。
5. GKE 是 Google 托管的 Kubernetes。
```

---

## 12. 小练习

### 问题 1

如果我部署了一个 `order-service`，希望它运行 3 个副本，应该配置在哪里？

答案：

```text
Deployment 的 replicas
```

---

### 问题 2

如果 Pod 重启后 IP 变了，其他服务应该通过什么访问它？

答案：

```text
Service
```

---

### 问题 3

如果我想查看某个 Pod 为什么启动失败，应该先看什么？

答案：

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

---

### 问题 4

如果我想把应用版本从 `v1` 更新到 `v2`，通常改哪里？

答案：

```text
Deployment 里的 container image
```

例如：

```yaml
image: order-service:v2
```

---

## 13. 下一节预告

下一节建议进入：

```text
Day 2 / Part 2：Pod、Deployment、Service 详细拆解
```

重点目标：

```text
用一个真实服务调用链讲清楚 Kubernetes 是怎么运行应用的。
```
