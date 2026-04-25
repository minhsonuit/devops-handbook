# SSL/TLS & Certificate Management

## SSL Hardening

Dat trong `server` block (hoac `http` block de ap dung chung):

```nginx
# Chi cho phep TLS 1.2 va 1.3
ssl_protocols TLSv1.2 TLSv1.3;

# Cipher suite an toan
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305;

# TLS 1.3 thi khong can dong nay, nhung cho TLS 1.2 thi nen bat
ssl_prefer_server_ciphers off;

# Session cache de giam handshake
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 1d;
ssl_session_tickets off;
```

### OCSP Stapling

Giup client xac minh cert nhanh hon, khong can goi rieng toi CA:

```nginx
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
```

### HTTP/2

```nginx
# Bat HTTP/2 tren HTTPS
listen 443 ssl http2;
```

Luu y: Tu Nginx 1.25.1, `http2 on;` la directive rieng thay vi nam trong `listen`.

```nginx
# Nginx >= 1.25.1
listen 443 ssl;
http2 on;
```

## Let's Encrypt voi Docker

### Cau truc thu muc

```text
/etc/letsencrypt/
├── live/
│   └── api-pos.pharmacity.vn/
│       ├── fullchain.pem    ← dung cho ssl_certificate
│       ├── privkey.pem      ← dung cho ssl_certificate_key
│       ├── cert.pem
│       └── chain.pem
└── renewal/
    └── api-pos.pharmacity.vn.conf
```

### Nginx config cho ACME challenge

```nginx
server {
    listen 80;
    server_name api-pos.pharmacity.vn;

    # Let's Encrypt verification
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    # Redirect tat ca request khac sang HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}
```

### Tao cert lan dau

```bash
docker run -it --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot certonly \
  --webroot -w /var/www/certbot \
  -d api-pos.pharmacity.vn \
  --email admin@pharmacity.vn \
  --agree-tos \
  --no-eff-email
```

### Renew cert

```bash
docker run --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot renew
```

### Auto renew bang cron

```bash
# Them vao crontab: chay 3h sang hang ngay
0 3 * * * docker run --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot renew \
  && docker exec NGINX_CONTAINER nginx -s reload
```

### Dry run (test khong tao cert that)

```bash
docker run --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot renew --dry-run
```

## Kiem tra certificate

### Xem thong tin cert

```bash
# Xem cert day du
docker exec NGINX_CONTAINER openssl x509 \
  -in /etc/letsencrypt/live/api-pos.pharmacity.vn/fullchain.pem \
  -text -noout

# Chi xem ngay het han
docker exec NGINX_CONTAINER openssl x509 \
  -in /etc/letsencrypt/live/api-pos.pharmacity.vn/fullchain.pem \
  -enddate -noout

# Xem subject va issuer
docker exec NGINX_CONTAINER openssl x509 \
  -in /etc/letsencrypt/live/api-pos.pharmacity.vn/fullchain.pem \
  -subject -issuer -noout
```

### Test SSL tu ben ngoai

```bash
# Kiem tra SSL connection
openssl s_client -connect api-pos.pharmacity.vn:443 \
  -servername api-pos.pharmacity.vn </dev/null 2>/dev/null | \
  openssl x509 -noout -dates

# Kiem tra bang curl
curl -vI https://api-pos.pharmacity.vn 2>&1 | grep -E 'SSL|certificate|expire|subject'

# Kiem tra TLS version
openssl s_client -connect api-pos.pharmacity.vn:443 \
  -servername api-pos.pharmacity.vn </dev/null 2>&1 | grep 'Protocol'
```

### Kiem tra cert sap het han (script)

```bash
#!/bin/bash
DOMAIN="api-pos.pharmacity.vn"
EXPIRY=$(echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null \
  | openssl x509 -noout -enddate | cut -d= -f2)
EXPIRY_EPOCH=$(date -d "$EXPIRY" +%s 2>/dev/null || date -j -f "%b %d %T %Y %Z" "$EXPIRY" +%s)
NOW_EPOCH=$(date +%s)
DAYS_LEFT=$(( (EXPIRY_EPOCH - NOW_EPOCH) / 86400 ))
echo "$DOMAIN: $DAYS_LEFT ngay con lai (het han: $EXPIRY)"
if [ $DAYS_LEFT -lt 14 ]; then
  echo "⚠️  CANH BAO: Cert sap het han!"
fi
```

## Troubleshooting SSL

| Loi | Nguyen nhan | Cach xu ly |
|-----|------------|------------|
| `ERR_CERT_DATE_INVALID` | Cert het han | Renew cert |
| `ERR_CERT_COMMON_NAME_INVALID` | Cert khong khop domain | Tao lai cert dung domain |
| `SSL_ERROR_RX_RECORD_TOO_LONG` | Port 443 khong co SSL | Kiem tra `listen 443 ssl` |
| `no shared cipher` | Cipher suite khong tuong thich | Kiem tra `ssl_ciphers` |
| `cannot load certificate` | Duong dan cert sai | Kiem tra path va permission |

```bash
# Debug SSL loi
docker exec NGINX_CONTAINER nginx -t 2>&1 | grep -i ssl
docker logs --since 5m NGINX_CONTAINER 2>&1 | grep -i ssl
```
