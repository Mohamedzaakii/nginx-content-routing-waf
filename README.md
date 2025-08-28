# Nginx Content Routing & WAF-Lite Gateway 

## Project Overview

This project demonstrates a professional **Nginx reverse proxy/load balancer** setup with advanced features:
 
- HTTPS termination (self-signed certificate)
- Path-based routing (`/api/` goes to backend 129)
- Load balancing across two backend servers using `least_conn`
- Proxy caching for static assets
- Per-IP rate limiting
- WAF-lite: basic blocking of common SQLi/XSS patterns
- Custom log format with upstream metadata
- Stub status endpoint `/nginx_status` for monitoring
- Fault tolerance with `proxy_next_upstream` and backend fail detection
- Custom error pages (50x)
- Log rotation configuration

**Deployed on 3 RHEL VMs:**
- Load Balancer / Nginx Gateway: `192.168.145.130`  
- Backend 1: `192.168.145.129`  
- Backend 2: `192.168.145.131` 

## Folder Structure

nginx-content-routing-waf/
├── README.md
├── lb/
│ ├── nginx.conf
│ └── conf.d/gateway.conf
├── backends/
│ ├── site_v1_index.html
│ ├── site_v2_index.html
│ └── somefile.js
├── docs/
│ ├── TEST_STEPS.md
│ └── CERTS_GUIDE.md
│ └── limits.yaml
└── logrotate/
└── nginx-custom


---

## Deployment Steps

```bash

1. Install Nginx on LB and backends:
sudo dnf -y install nginx openssl
sudo systemctl enable --now nginx

2. Place configuration files

- LB VM:

sudo cp lb/nginx.conf /etc/nginx/nginx.conf
sudo cp lb/conf.d/gateway.conf /etc/nginx/conf.d/gateway.conf
sudo nginx -t && sudo systemctl restart nginx

- Backends (129 & 131):
 
sudo mkdir -p /usr/share/nginx/html
sudo cp backends/site_v1_index.html /usr/share/nginx/html/index.html  # backend 129
sudo cp backends/site_v2_index.html /usr/share/nginx/html/index.html  # backend 131
sudo cp backends/somefile.js /usr/share/nginx/html/
sudo nginx -t && sudo systemctl reload nginx

3. Create self-signed certificate

sudo mkdir -p /etc/ssl/nginx
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/nginx/nginx.key \
    -out /etc/ssl/nginx/nginx.crt \
    -subj "/CN=nginx-demo"

- Ensure permissions:

sudo chown root:root /etc/ssl/nginx/nginx.*
sudo chmod 600 /etc/ssl/nginx/nginx.key
sudo chmod 644 /etc/ssl/nginx/nginx.crt

## Testing / Verification

- HTTPS & backend routing:

curl -vk -H "Host: site1.local" https://192.168.145.130/api/

- Load balancing:

curl -vk -H "Host: site1.local" https://192.168.145.130/

- Proxy caching:

curl -sSI -k https://192.168.145.130/somefile.js | egrep 'X-Cache-Status|HTTP/'

- WAF-lite rule:

curl -vk -G -s -k "https://192.168.145.130/?q=select+from"

- Rate limiting: 

for i in {1..20}; do curl -s -k https://192.168.145.130/; done
