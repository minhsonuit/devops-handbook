# Nginx Config Guide

## Khung config co ban

```nginx
worker_processes auto;

events {
    worker_connections 4096;
}

http {
    upstream app_backend {
        server app:5001;
        keepalive 32;
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://app_backend;
        }
    }
}
```

## Reverse proxy nen co gi

```nginx
location / {
    proxy_pass http://app_backend;
    proxy_redirect off;
    proxy_http_version 1.1;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header Connection "";
}
```

## Khi nao dung upstream

Nen dung `upstream` khi:

- Muon dat ten de de hieu
- Muon doi backend ma khong sua nhieu `location`
- Muon dung keepalive voi backend
- Muon scale nhieu backend

Vi du:

```nginx
upstream frontend {
    server 10.1.1.176:8000;
    keepalive 32;
}
```

## Khi nao route bang path

Dung `location` khi:

- Muon public path rieng nhu `/portal/`
- Muon whitelist IP cho mot nhom path
- Muon rewrite truoc khi vao backend

Vi du:

```nginx
location /portal/ {
    allow 10.1.0.0/16;
    allow 10.2.0.0/16;
    deny all;
    rewrite ^/portal/?(.*)$ /$1 break;
    proxy_pass http://frontend;
}
```

## Best practices khi viet config

- Uu tien `upstream` thay vi hard-code IP trong nhieu noi
- Gom header proxy dung chung neu co the
- Khong set cung mot header 2 lan voi 2 gia tri mau thuan
- Khong mo `stub_status` ra public
- Dung `server_name` va cert dung domain production
- Neu public duoi prefix nhu `/portal/`, frontend phai support base path hoac can co route asset bo sung
