# Nginx Operations Checklist

## Truoc khi sua config

- Backup file `nginx.conf`
- Xac dinh file active that su dang duoc mount
- Xem config hien tai bang `nginx -T`

## Sau khi sua config

```bash
docker exec 82197eccd1ff nginx -t
docker exec 82197eccd1ff nginx -s reload
```

## Sau khi reload

- Test URL chinh
- Test path moi nhu `/portal/`
- Xem log 5-10 phut gan nhat
- Xac nhan JS/CSS load du neu la frontend

Lenh nhanh:

```bash
docker service logs --since 10m pharmacity_reverseproxy
docker logs --since 10m 82197eccd1ff
docker logs --since 10m 82197eccd1ff 2>&1 | grep '/portal/'
```

## Checklist review config

- `server_name` dung domain production
- cert va key dung domain production
- `stub_status` khong mo public
- timeout da dat ro rang
- khong co `proxy_set_header` mau thuan
- path proxy co dung voi backend
- neu dung prefix nhu `/portal/`, frontend co support base path hoac co route asset phu hop

## Checklist khi gap loi

- `403`: xem `allow/deny`, `real_ip`, IP nguon that su
- `404`: xem path rewrite, asset path, backend route
- `502`: xem upstream song khong, `connect() failed`, timeout
- `504`: xem backend cham, `proxy_read_timeout`, `upstream timed out`
- Trang trang: xem asset path, base path frontend, JS/CSS co 404 khong
