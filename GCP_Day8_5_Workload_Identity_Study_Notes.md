# GCP 学习路线 - Day 8.5

# Cloud Storage 权限控制、IAM、Service Account 与 Workload Identity

## 学习目标

- 理解 Bucket 权限控制
- 理解 IAM 与 Cloud Storage
- 理解 Service Account
- 理解 Workload Identity
- 理解 Signed URL
- 理解企业级权限设计

---

## Cloud Storage 默认安全模型

Google Cloud 采用：

Deny By Default

默认拒绝访问。

即使 Pod 存在：

也不能直接访问 Bucket。

必须显式授权。

---

## Bucket 也是 Resource

Bucket 属于 Google Cloud Resource。

因此可以绑定 IAM Role。

常见角色：

roles/storage.objectViewer

允许：

- 查看文件
- 下载文件

---

roles/storage.objectAdmin

允许：

- 上传
- 下载
- 删除

---

## 谁获得权限

很多人误以为：

Pod 获得权限。

实际上：

Google Service Account（GSA）

才是真正的 IAM 身份。

---

## 企业真实案例

系统：

document-platform

服务：

- web-service
- report-service
- admin-service

Bucket：

company-prod-documents

存储：

- report.pdf
- contract.pdf
- invoice.pdf

---

## Google Service Account 设计

上传服务：

gsa-report-uploader@company-prod.iam.gserviceaccount.com

权限：

roles/storage.objectAdmin

---

阅读服务：

gsa-document-reader@company-prod.iam.gserviceaccount.com

权限：

roles/storage.objectViewer

---

## Kubernetes Service Account

KSA：

report-service-ksa

对应：

report-service

---

KSA：

web-service-ksa

对应：

web-service

---

## Workload Identity

Google 推荐方案：

KSA

↓

Workload Identity

↓

GSA

↓

Cloud Storage

---

示例：

report-service Pod

↓

report-service-ksa

↓

Workload Identity

↓

gsa-report-uploader

↓

Cloud Storage

---

web-service Pod

↓

web-service-ksa

↓

Workload Identity

↓

gsa-document-reader

↓

Cloud Storage

---

## 为什么不用 JSON Key

传统方式：

GSA

↓

JSON Key

↓

Kubernetes Secret

↓

Pod

↓

Cloud Storage

---

风险：

- Key 泄漏
- 生命周期过长
- 难管理

---

## Workload Identity 优势

无长期密钥

---

自动轮换 Token

---

支持最小权限原则

---

支持审计

Cloud Audit Log 可以追踪访问行为。

---

## 实际效果

report-service：

可上传

可删除

可下载

---

web-service：

可下载

不可删除

删除返回：

403 Permission Denied

---

## Signed URL

问题：

用户浏览器如何访问私有 Bucket 文件？

解决方案：

Signed URL

---

本质：

临时授权链接

---

例如：

有效期：

10分钟

---

10分钟内可访问。

之后自动失效。

---

## 企业上传最佳实践

传统：

Browser

↓

Pod

↓

Bucket

---

推荐：

Browser

↓

Pod

↓

生成 Signed URL

↓

Browser

↓

Bucket

---

优势：

- 减少 Pod 压力
- 提升上传速度
- 降低成本

---

## 企业标准权限模型

gsa-payment-service

权限：

- Cloud SQL Client
- Pub/Sub Publisher

---

gsa-report-service

权限：

- Storage Object Admin

---

gsa-web-service

权限：

- Storage Object Viewer

---

gsa-monitoring-agent

权限：

- logging.logWriter
- monitoring.metricWriter

---

原则：

一个服务对应一个 GSA。

---

## 核心架构图

Pod

↓

Kubernetes Service Account

↓

Workload Identity

↓

Google Service Account

↓

IAM Role

↓

Bucket

---

## 面试高频题

Q1

Pod 是否直接拥有 Bucket 权限？

不是。

GSA 才拥有权限。

---

Q2

什么是 Workload Identity？

KSA 与 GSA 的映射机制。

---

Q3

为什么不推荐 JSON Key？

存在泄漏风险。

---

Q4

什么是 Signed URL？

临时授权访问链接。

---

Q5

为什么企业喜欢 Workload Identity？

更安全、无需长期密钥、支持审计。

---

## Day8.5 核心记忆点

Bucket

↓

IAM

↓

GSA

↓

Workload Identity

↓

KSA

↓

Pod

---

不要把 JSON Key 放进 Secret。

优先使用：

Workload Identity

---

## Day8.5 总结

Workload Identity allows GKE Pods to securely access Google Cloud services without storing long-lived service account keys.

翻译：

Workload Identity 允许 GKE Pod 在不保存长期 Service Account Key 的情况下安全访问 Google Cloud 服务。
