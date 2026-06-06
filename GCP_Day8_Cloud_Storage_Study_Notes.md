# GCP 学习路线 - Day 8

# Cloud Storage（GCP 对象存储）

## 学习目标

- 理解 Cloud Storage
- 理解 Bucket 和 Object
- 理解对象存储
- 理解数据库与文件存储分离
- 理解 Storage Class
- 理解 GKE 与 Cloud Storage 集成

---

## 什么是 Cloud Storage

Cloud Storage（GCS）是 Google Cloud 的对象存储服务。

简单理解：

Compute Engine = 云服务器

Cloud Storage = 云文件存储

适用于：

- 图片
- 视频
- PDF
- Word
- Excel
- 日志
- 备份
- AI训练数据

---

## 为什么需要 Cloud Storage

如果把图片、视频直接存数据库：

- 数据库容量迅速膨胀
- Backup 变慢
- Restore 变慢
- 查询性能下降

因此企业通常采用：

数据库存元数据

Cloud Storage 存文件

---

## Cloud Storage 核心结构

Cloud Storage

↓

Bucket

↓

Object

---

## Bucket

Bucket 可以理解为：

存储桶

或者：

顶级文件容器

例如：

- company-images
- company-pdf
- company-backup

---

## Object

Object 就是文件。

例如：

- avatar.jpg
- report.pdf
- backup.tar.gz

---

## 关系示例

Cloud Storage

├── company-images

│   ├── avatar1.jpg

│   ├── avatar2.jpg

│   └── logo.png

│

├── company-pdf

│   ├── report.pdf

│   └── contract.pdf

│

└── company-backup

    └── db-backup.tar.gz

---

## 仓库模型理解

Cloud Storage = 仓库园区

Bucket = 仓库

Object = 仓库里的货物

关系：

Cloud Storage

↓

Bucket

↓

Object

---

## PostgreSQL 与 Cloud Storage

### PostgreSQL

适合存储：

- 用户信息
- 订单信息
- 产品信息
- 文件路径

示例：

users

id

name

avatar_url

---

### Cloud Storage

适合存储：

- 图片
- 视频
- PDF
- Word
- Excel
- 备份文件

---

## 企业最佳实践

数据库：

存元数据

Cloud Storage：

存实际文件

例如：

users

id

name

avatar_url

↓

gs://company-images/avatar1.jpg

真正文件：

Cloud Storage

↓

company-images

↓

avatar1.jpg

---

## GKE 上传文件流程

用户上传：

photo.jpg

流程：

Browser

↓

GKE Pod

↓

Cloud Storage

数据库保存：

文件名

Bucket

URL

上传时间

---

## Cloud Storage 与文件系统区别

本地磁盘：

/data/images/avatar.jpg

拥有真实目录。

---

Cloud Storage：

images/avatar.jpg

只是 Object Name。

没有真正目录结构。

---

## Storage Class

核心原则：

访问越少

↓

存储越便宜

↓

读取越贵

---

### Standard

高频访问

例如：

- 用户头像
- 网站图片
- 热数据

---

### Nearline

低频访问

例如：

- 月报
- 最近日志

---

### Coldline

很少访问

例如：

- 半年备份

---

### Archive

极少访问

例如：

- 审计数据
- 历史归档

---

## 生命周期管理

示例：

最近30天：

Standard

↓

30天后：

Nearline

↓

180天后：

Coldline

↓

1年后：

Archive

Google 自动迁移。

---

## 企业架构中的位置

User

↓

Load Balancer

↓

Ingress

↓

Service

↓

Pod

├── Cloud SQL

└── Cloud Storage

Cloud SQL：

存业务数据

Cloud Storage：

存文件

---

## 面试高频题

Q1

什么是 Cloud Storage？

Google Cloud 的对象存储服务。

---

Q2

什么是 Bucket？

存储对象的顶级容器。

---

Q3

什么是 Object？

存储在 Bucket 中的文件。

---

Q4

为什么图片不放数据库？

会导致数据库膨胀和性能下降。

---

Q5

数据库和 Cloud Storage 如何配合？

数据库存元数据。

Cloud Storage 存实际文件。

---

## Day8 核心记忆点

Cloud Storage = 对象存储

Bucket = 存储桶

Object = 文件

Cloud Storage → Bucket → Object

数据库存元数据

Cloud Storage 存文件

访问越少，存储越便宜

---

## Day8 总结

Cloud Storage 负责存储文件。

数据库负责存储文件相关信息。

标准模式：

Cloud Storage

↓

Bucket

↓

Object

数据库

↓

文件 URL
