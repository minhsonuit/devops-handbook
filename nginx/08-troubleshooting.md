# Nginx Troubleshooting

## Decision tree theo HTTP status

### 502 Bad Gateway

```
502 → Backend chay khong? → KHONG → Khoi dong backend
                          → CO → DNS resolve? → KHONG → Kiem tra docker network
                                              → CO → Port dung? → Xem error log
```

```bash
# Backend song khong?
docker exec NGINX sh -c "curl -s -o /dev/null -w '%{http_code}' http://app:5001/health"

# DNS resolve?
docker exec NGINX sh -c "getent hosts app"

# Error log
docker logs --since 10m NGINX 2>&1 | grep -Ei 'connect|upstream|refused|timed out'
```

### 504 Gateway Timeout

```bash
# Xem upstream response time
docker logs --since 30m NGINX 2>&1 | awk 'match($0,/urt=([0-9.]+)/,a){print a[1]}' | sort -n | tail -10

# Kiem tra timeout hien tai
docker exec NGINX nginx -T | grep -i timeout
```

Fix: tang timeout cho path cu the:

```nginx
location /api/export/ {
    proxy_read_timeout 300s;
    proxy_send_timeout 300s;
    proxy_pass http://web_api/api/export/;
}
```

### 403 Forbidden

```bash
# Xem rule allow/deny
docker exec NGINX nginx -T | grep -B2 -A10 'PROBLEM_PATH'

# Xem IP client
docker logs --since 5m NGINX 2>&1 | grep '403' | head -10
```

Fix khi sau Load Balancer:

```nginx
set_real_ip_from 10.0.0.0/8;
set_real_ip_from 172.16.0.0/12;
real_ip_header X-Forwarded-For;
real_ip_recursive on;
```

### 404 Not Found

```bash
# Xem tat ca location
docker exec NGINX nginx -T | grep 'location'

# Test truc tiep vao backend
docker exec NGINX sh -c "curl -s -o /dev/null -w '%{http_code}' http://app:5001/YOUR_PATH"
```

Fix cho SPA frontend:

```nginx
location /ui/ {
    alias /var/www/ui/;
    try_files $uri $uri/ /ui/index.html;
}
```

### 413 Request Entity Too Large

```nginx
# Trong http block
client_max_body_size 20m;

# Hoac cho endpoint cu the
location /api/upload/ {
    client_max_body_size 100m;
    proxy_pass http://web_api/api/upload/;
}
```

### 499 Client Closed Connection

Client ngat truoc khi Nginx tra response. Thuong do backend xu ly lau.

```bash
docker logs --since 1h NGINX 2>&1 | grep '" 499 ' | wc -l
```

## Kiem tra nhanh toan bo he thong

```bash
# 1. Config ok?
docker exec NGINX nginx -t

# 2. Error gan day
docker logs --since 30m NGINX 2>&1 | grep -Ei 'error|warn|crit|emerg' | tail -20

# 3. Request loi
docker logs --since 30m NGINX 2>&1 | grep -E '" [45][0-9]{2} ' | tail -20

# 4. Request cham nhat
docker logs --since 30m NGINX 2>&1 | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -10

# 5. Backend reachable?
docker exec NGINX sh -c "curl -s -o /dev/null -w '%{http_code}' http://app:5001/health"

# 6. File descriptor
docker exec NGINX sh -c "cat /proc/1/limits | grep 'open files'"
```

## Debug DNS & config

```bash
docker exec NGINX sh -c "getent hosts app"
docker exec NGINX sh -c "cat /etc/resolv.conf"
docker exec NGINX nginx -T | grep -A3 'upstream'
docker exec NGINX nginx -v
docker exec NGINX nginx -V 2>&1 | tr ' ' '\n' | grep module
```
