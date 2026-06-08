# GCP 学习路线 - Day 14

# Cloud Armor（Google WAF）与 OWASP Top10

## 核心内容

Cloud Armor = Google Cloud WAF

涵盖：
- DDoS Protection
- IP Blocking
- Geo Blocking
- Rate Limiting
- OWASP Protection
- Bot Protection
- Adaptive Protection

---

## 架构位置

Internet
↓
Cloud Armor
↓
HTTPS Load Balancer
↓
Ingress
↓
Service
↓
Pod
↓
Cloud SQL

---

## DDoS

利用海量请求耗尽系统资源。

正常：100 req/s

攻击：10000+ req/s

Cloud Armor 在流量进入系统前进行拦截。

---

## Rate Limiting

限制访问频率。

例如：

/login

100 req/min/IP

超过后返回：429 Too Many Requests

---

## OWASP Top10

重点掌握：

- SQL Injection
- XSS
- CSRF
- SSRF

---

## SQL Injection

目标：数据库

记忆：骗数据库

典型攻击：

' OR 1=1 --

---

## XSS

目标：浏览器

记忆：骗浏览器

典型攻击：

<script>alert('hack')</script>

目的：窃取 Cookie、Token

---

## CSRF

目标：用户身份

记忆：骗用户身份

利用用户已登录状态执行操作。

---

## SSRF

目标：服务器

记忆：骗服务器发请求

危险链路：

SSRF
↓
Metadata Server
↓
Service Account Token
↓
Cloud Resource Access

---

## Bot Protection

识别：

Human
↓
Allow

Bot
↓
Challenge / Block

---

## Adaptive Protection

学习正常流量模式。

发现异常后自动建议或执行防护规则。

---

## 面试记忆

Cloud Armor 为什么放在最前面？

为了让恶意流量在进入 Load Balancer、Ingress、Pod 和 Cloud SQL 之前被拦截，避免消耗系统资源并降低攻击影响。

---

## Day14 总结

Cloud Armor 是 GCP 的 Web Application Firewall（WAF），负责在流量进入系统前完成安全检查，保护应用和云资源。