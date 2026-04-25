# Gzip & Performance

## Bat gzip compression

Dat trong `http` block:

```nginx
gzip on;
gzip_vary on;
gzip_proxied any;
gzip_comp_level 4;
gzip_min_length 256;
gzip_types
    text/plain
    text/css
    text/javascript
    application/javascript
    application/json
    application/xml
    image/svg+xml
    application/x-font-ttf
    font/opentype;
```

### Y nghia cac thong so

| Thong so | Y nghia |
|----------|---------|
| `gzip_vary on` | Them header `Vary: Accept-Encoding` de cache proxy xu ly dung |
| `gzip_proxied any` | Nen ca response tu upstream |
| `gzip_comp_level 4` | Muc nen 1-9, 4-6 la can bang tot giua CPU va kich thuoc |
| `gzip_min_length 256` | Khong nen file nho hon 256 bytes (overhead > loi ich) |

### Kiem tra gzip co hoat dong khong

```bash
# Kiem tra response co gzip
curl -sI -H "Accept-Encoding: gzip" https://your-domain.com/api/test | grep -i content-encoding

# So sanh kich thuoc voi va khong gzip
curl -s -H "Accept-Encoding: gzip" -o /dev/null -w "gzip: %{size_download}\n" https://your-domain.com/api/test
curl -s -o /dev/null -w "raw: %{size_download}\n" https://your-domain.com/api/test
```

## Static file caching

```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
    expires 30d;
    add_header Cache-Control "public, immutable";
    access_log off;
}
```

## Buffer tuning

Mac dinh Nginx buffer nho, co the gay cham voi response lon:

```nginx
# Trong http hoac server block
proxy_buffer_size 128k;
proxy_buffers 4 256k;
proxy_busy_buffers_size 256k;

# Cho truong hop response rat lon (export, report)
location /api/export/ {
    proxy_buffer_size 256k;
    proxy_buffers 8 512k;
    proxy_busy_buffers_size 512k;
    proxy_read_timeout 300s;
    proxy_pass http://web_api/api/export/;
}
```

## Network tuning

```nginx
sendfile on;        # Dung kernel sendfile, nhanh hon read+write
tcp_nopush on;      # Gom headers va body vao 1 TCP packet
tcp_nodelay on;     # Gui ngay, khong doi buffer day (tot cho keepalive)
```

## Keepalive tuning

### Client keepalive

```nginx
keepalive_timeout 15s;      # Giu connection client mo trong 15s
keepalive_requests 1000;    # Cho phep 1000 request tren 1 connection
```

### Upstream keepalive

```nginx
upstream web_api {
    server app:5001;
    keepalive 64;           # Giu 64 idle connections toi backend
}
```

Luu y: de upstream keepalive hoat dong, can:

```nginx
proxy_http_version 1.1;
proxy_set_header Connection "";
```

## Khi nao can tuning

| Trieu chung | Kiem tra | Huong xu ly |
|-------------|----------|-------------|
| Response lon ma cham | `proxy_buffers` | Tang buffer |
| Bandwidth cao | `gzip` | Bat gzip |
| Nhieu reconnect | `keepalive` | Tang keepalive |
| CPU Nginx cao | `gzip_comp_level` | Giam level hoac offload |
| Static file cham | `expires`, `sendfile` | Bat cache + sendfile |
