## Self-Signed Certificate
```bash
sudo mkdir -p /etc/ssl/nginx/
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/nginx/nginx.key \
    -out /etc/ssl/nginx/nginx.crt \
    -subj "/CN=nginx-demo"
