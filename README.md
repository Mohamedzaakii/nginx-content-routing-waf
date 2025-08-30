# 🚀 Nginx Content Routing & WAF-Lite Gateway

A professional **Nginx Reverse Proxy & Load Balancer** setup with advanced gateway features for security, performance, and monitoring.

---

## ✨ Features
- 🔒 **HTTPS termination** (self-signed TLS certificate)  
- 🔀 **Path-based routing** (`/api/` → backend 129)  
- ⚖️ **Load balancing** (`least_conn` algorithm)  
- ⚡ **Proxy caching** for static assets  
- 🛡️ **Rate limiting** per IP  
- 🔍 **WAF-lite** filtering for basic SQLi/XSS patterns  
- 📊 **Custom log format** with upstream metadata  
- 📡 **Stub status endpoint** (`/nginx_status`) for monitoring  
- ♻️ **Fault tolerance** with `proxy_next_upstream` + backend failover  
- 🎨 **Custom error pages (50x)**  
- 🗂️ **Log rotation** for Nginx logs  

---

## 🏗️ Architecture

**Deployment on 3 RHEL VMs:**
- **Load Balancer / Gateway:** `192.168.145.130`
- **Backend 1:** `192.168.145.129`
- **Backend 2:** `192.168.145.131`

```text
           ┌──────────────┐
           │   Clients    │
           └──────┬───────┘
                  │
                  ▼
          ┌─────────────────┐
          │ Nginx Gateway   │
          │ 192.168.145.130 │
          └──────┬───────┬──┘
                 │       │
   ┌─────────────┘       └─────────────┐
   ▼                                   ▼
Backend 1                          Backend 2
192.168.145.129                   192.168.145.131

```
## 📂 Project Structure


```text
nginx-content-routing-waf/
├── README.md
├── lb/
│   ├── nginx.conf
│   └── conf.d/gateway.conf
├── backends/
│   ├── site_v1_index.html
│   ├── site_v2_index.html
│   └── somefile.js
├── scripts/
│   ├── setup_lb.sh
│   ├── setup_backend1.sh
│   ├── setup_backend2.sh
│   └── generate_certs.sh
├── docs/
│   ├── TEST_STEPS.md
│   ├── CERTS_GUIDE.md
│   └── limits.yaml
└── logrotate/
    └── nginx-custom

```

---

## ⚙️ Deployment

Deployment is automated via scripts.

### 1️⃣ Install and Configure Nginx

Run on **Load Balancer**:
```bash
./scripts/setup_lb.sh
```
Run on Backend 1 (129):
```bash
./scripts/setup_backend1.sh
```
Run on Backend 2 (131):
```bash
./scripts/setup_backend2.sh
```
2️⃣ Generate Self-Signed TLS Certificate
```bash
./scripts/generate_certs.sh
```
---

## 🧪 Testing & Verification
  - HTTPS & Backend Routing
    ```bash
    curl -vk -H "Host: site1.local" https://192.168.145.130/api/
    ```
  - Load Balancing
    ```bash
    curl -vk -H "Host: site1.local" https://192.168.145.130/
    ```
  - Proxy Caching
    ```bash
    curl -sSI -k https://192.168.145.130/somefile.js | egrep 'X-Cache-Status|HTTP/'
    ```
  - WAF-lite Filtering
    ```bash
    curl -vk -G -s -k "https://192.168.145.130/?q=select+from"
    ```
  - Rate Limiting
    ```bash
    for i in {1..20}; do curl -s -k https://192.168.145.130/; done

---

## 📜 Documentation
  - TEST_STEPS.md
  - CERTS_GUIDE.md

  









