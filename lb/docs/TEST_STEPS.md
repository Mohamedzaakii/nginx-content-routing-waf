# Test Steps for Nginx LB Project

## HTTPS Test
```bash
curl -vk -H "Host: site1.local" https://192.168.145.130/api/

## Load Balancing Test
curl -vk -H "Host: site1.local" https://192.168.145.130/

## Proxy Caching Test
curl -sSI -k https://192.168.145.130/somefile.js | egrep 'X-Cache-Status|HTTP/'

## WAF-lite Test
curl -vk -G -s -k "https://192.168.145.130/?q=select+from"

## Rate Limiting Test
for i in {1..20}; do curl -s -k https://192.168.145.130/; done

 
