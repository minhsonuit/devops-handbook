# Nginx Tuning and Parameters

## Nhom thong so can quan tam

- `worker_processes`
- `worker_connections`
- `worker_rlimit_nofile`
- `keepalive_timeout`
- `keepalive_requests`
- `proxy_connect_timeout`
- `proxy_read_timeout`
- `proxy_send_timeout`
- `client_max_body_size`

## Goi y gia tri co ban

```nginx
worker_processes auto;
worker_rlimit_nofile 65535;

events {
    worker_connections 4096;
    multi_accept on;
}

http {
    keepalive_timeout 15s;
    keepalive_requests 1000;
    client_max_body_size 20m;

    proxy_connect_timeout 5s;
    proxy_send_timeout 60s;
    proxy_read_timeout 60s;
}
```

## Y nghia nhanh

### `worker_processes`

- `auto` thuong la lua chon hop ly
- tan dung so core CPU

### `worker_connections`

- so connection toi da moi worker co the giu
- qua thap de gap nghen connection khi spike
- qua cao ma khong du file descriptor cung vo ich

### `worker_rlimit_nofile`

- tang gioi han file descriptor
- nen di cung `worker_connections`

### `keepalive_timeout`

- giu connection client mo trong bao lau
- qua cao co the ton tai nguyen
- qua thap co the tang reconnect

### `proxy_*_timeout`

- kiem soat timeout khi Nginx noi voi backend
- rat quan trong de debug `502`, `504`, `upstream timed out`

### `client_max_body_size`

- anh huong upload
- neu qua nho de gap `413`

## Yeu to anh huong den hieu nang

- So client dong thoi
- Ty le keepalive
- So replica backend
- Toc do backend phan hoi
- Kich thuoc response
- So luong request lau nhu webhook, export, upload
- Gioi han CPU/RAM cua container
- Gioi han file descriptor cua host va container

## Cach nghi khi tuning

- Gap `worker_connections are not enough`: xem lai `worker_connections`, `worker_rlimit_nofile`, keepalive
- Gap `upstream timed out`: xem lai backend, `proxy_read_timeout`, request time
- Gap `too many open files`: xem lai nofile cua host/container va so connection
- Gap request cham: xem `request_time`, `upstream_response_time`, CPU va RAM
