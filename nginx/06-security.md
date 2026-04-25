# Nginx Security

## An version Nginx

Nginx mac dinh tra ve version trong response header va trang loi.
Nen tat di de han che thong tin lo ra ngoai.

```nginx
server_tokens off;
```

Kiem tra:

```bash
curl -sI https://your-domain.com | grep -i server
# Nen thay: Server: nginx
# Khong nen thay: Server: nginx/1.25.3
```

## Security headers

Dat trong `server` block hoac `http` block:

```nginx
# Chong MIME-type sniffing
add_header X-Content-Type-Options "nosniff" always;

# Chong clickjacking
add_header X-Frame-Options "SAMEORIGIN" always;

# Chong XSS co ban (browser cu)
add_header X-XSS-Protection "1; mode=block" always;

# Kiem soat referrer
add_header Referrer-Policy "strict-origin-when-cross-origin" always;

# Bat buoc HTTPS (HSTS)
# Chi bat khi da chac chan HTTPS chay on dinh
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

Kiem tra:

```bash
curl -sI https://your-domain.com | grep -iE 'x-content|x-frame|x-xss|referrer|strict'
```

## Rate limiting

Rate limiting giup chong brute force, DDoS nhe, va bot spam.

### Cau hinh trong `http` block

```nginx
http {
    # Zone cho API: 30 request/s moi IP
    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=30r/s;

    # Zone cho login: 5 request/s moi IP
    limit_req_zone $binary_remote_addr zone=login_limit:10m rate=5r/s;

    # Gioi han so connection dong thoi moi IP
    limit_conn_zone $binary_remote_addr zone=conn_limit:10m;
}
```

### Ap dung trong `location`

```nginx
location /api/ {
    limit_req zone=api_limit burst=50 nodelay;
    limit_conn conn_limit 100;
    proxy_pass http://web_api/;
}

location /api/auth/login {
    limit_req zone=login_limit burst=10 nodelay;
    proxy_pass http://web_api/api/auth/login;
}
```

### Y nghia cac thong so

| Thong so | Y nghia |
|----------|---------|
| `rate=30r/s` | Cho phep 30 request/giay binh thuong |
| `burst=50` | Cho phep spike len 50 truoc khi bat dau reject |
| `nodelay` | Xu ly burst ngay, khong xep hang doi |
| `zone=api_limit:10m` | Luu trang thai trong 10MB shared memory (~160,000 IP) |

### Khi bi rate limit, client nhan HTTP 503. Doi thanh 429:

```nginx
limit_req_status 429;
limit_conn_status 429;
```

## Whitelist IP dung cach

```nginx
# SAI — allow all vo hieu hoa toan bo ACL
location /admin/ {
    allow 10.1.0.0/16;
    allow 10.2.0.0/16;
    allow all;              # ← Dong nay lam mat tac dung whitelist
    proxy_pass http://web_api/admin/;
}

# DUNG — deny all o cuoi
location /admin/ {
    allow 10.1.0.0/16;
    allow 10.2.0.0/16;
    deny all;               # ← Chan tat ca IP khong nam trong allow
    proxy_pass http://web_api/admin/;
}
```

Luu y: Nginx xu ly `allow/deny` theo thu tu tu tren xuong, match dau tien thang.

## Bao ve stub_status

```nginx
# SAI — mo public
location /nginx_status {
    stub_status;
}

# DUNG — chi cho internal access
location /nginx_status {
    stub_status;
    allow 127.0.0.1;
    allow 10.0.0.0/8;
    deny all;
}
```

## Chan request khong co Host header (default server)

```nginx
# Tra ve 444 (dong connection, khong response) cho request khong khop server_name
server {
    listen 80 default_server;
    listen 443 ssl default_server;
    server_name _;
    ssl_certificate     /etc/ssl/certs/dummy.crt;
    ssl_certificate_key /etc/ssl/private/dummy.key;
    return 444;
}
```

## Chan bot / scanner

```nginx
# Chan user-agent bot pho bien (dat trong server block)
if ($http_user_agent ~* (bot|crawl|spider|scraper|masscan|nikto|sqlmap)) {
    return 403;
}
```

Luu y: `if` trong Nginx co nhieu gotcha. Chi dung o muc don gian nhu tren.

## Kiem tra bao mat nhanh

```bash
# Kiem tra header
curl -sI https://your-domain.com | head -20

# Kiem tra stub_status co mo public khong
curl -s http://your-domain.com/nginx_status

# Kiem tra server token
curl -sI https://your-domain.com | grep -i server

# Kiem tra rate limit
for i in $(seq 1 100); do
  curl -s -o /dev/null -w "%{http_code}\n" https://your-domain.com/api/test
done | sort | uniq -c
```
