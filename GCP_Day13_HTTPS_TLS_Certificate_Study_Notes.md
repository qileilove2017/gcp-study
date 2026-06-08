# GCP 学习路线 - Day 13

# HTTPS、TLS、SSL Certificate 与 Google Managed Certificate

## 学习目标

- HTTP 与 HTTPS
- TLS
- SSL Certificate
- CA
- Google Managed Certificate
- TLS Handshake
- TLS Termination
- End-to-End TLS

---

## HTTP 与 HTTPS

HTTP：

明文传输。

HTTPS：

加密传输。

HTTPS 提供：

1. Encryption（加密）
2. Authentication（身份认证）
3. Integrity（完整性）

---

## SSL Certificate

证书相当于服务器身份证。

浏览器会验证：

- 是否过期
- 是否可信
- 域名是否匹配

验证通过后才会建立 HTTPS 连接。

---

## 证书过期事故

即使：

- Pod 正常
- Cloud SQL 正常
- Load Balancer 正常

如果证书过期：

用户仍然无法正常访问网站。

常见错误：

NET::ERR_CERT_DATE_INVALID

---

## CA（Certificate Authority）

证书颁发机构。

常见：

- Let's Encrypt
- DigiCert
- GlobalSign

浏览器通过 CA 信任链验证证书。

---

## Google Managed Certificate

Google 自动：

- 申请证书
- 安装证书
- 续期证书

降低运维风险。

---

## 企业真实案例

shop.company.com

↓

Cloud DNS

↓

34.120.88.10

↓

HTTPS Load Balancer

↓

Google Managed Certificate

↓

Ingress

↓

Service

↓

Pod

---

## 域名不匹配错误

错误：

NET::ERR_CERT_COMMON_NAME_INVALID

原因：

访问域名与证书中的域名不一致。

例如：

证书：

shop.company.com

访问：

api.company.com

---

## TLS Handshake

访问：

https://shop.company.com

流程：

Browser

↓

Certificate Validation

↓

TLS Handshake

↓

Encrypted HTTPS Connection

↓

业务请求

---

## TLS Termination

默认 GCP 架构：

Browser

↓

HTTPS

↓

Google HTTPS Load Balancer

↓

HTTP

↓

Ingress

↓

Service

↓

Pod

TLS 在 HTTPS Load Balancer 终止。

又称：

SSL Offloading

---

## 为什么在 Load Balancer 终止 TLS

TLS 握手消耗 CPU。

Google Load Balancer 统一处理：

- 证书验证
- TLS 握手
- HTTPS 解密

Pod 专注业务逻辑。

---

## End-to-End TLS

高安全场景：

Browser

↓

HTTPS

↓

Load Balancer

↓

HTTPS

↓

Ingress

↓

HTTPS

↓

Pod

全链路加密。

---

## 企业完整 HTTPS 链路

Browser

↓

Cloud DNS

↓

HTTPS Load Balancer

↓

Certificate Validation

↓

TLS Handshake

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

Q1 HTTPS 比 HTTP 多什么？

加密、身份认证、完整性。

Q2 什么是 SSL Certificate？

服务器身份证。

Q3 浏览器验证什么？

过期时间、可信度、域名匹配。

Q4 什么是 CA？

证书颁发机构。

Q5 Google Managed Certificate 的作用？

自动申请、安装、续期。

Q6 TLS Termination 在哪里？

默认在 HTTPS Load Balancer。

Q7 什么是 End-to-End TLS？

Browser 到 Pod 全链路加密。

---

## Day13 核心记忆

HTTPS = TLS + Certificate + Encryption + Authentication + Integrity

Certificate = 服务器身份证

Google Managed Certificate = 自动管理证书

TLS Termination = Load Balancer 解密 HTTPS

End-to-End TLS = Browser 到 Pod 全程加密

---

## Day13 总结

HTTPS 基于 TLS，提供加密、身份认证和完整性保护。

在 GCP 中，TLS 通常终止于 HTTPS Load Balancer，由其统一处理证书和安全连接。
