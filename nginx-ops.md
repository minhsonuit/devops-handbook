# Nginx Ops

File nay gom cac lenh de:

- Xem config Nginx
- Validate format va syntax
- Reload Nginx
- Chay truc tiep trong Docker container

Gia su container Nginx cua ban la:

```bash
82197eccd1ff
```

## Xem config Nginx

### Xem file config chinh

```bash
docker exec -it 82197eccd1ff sh
cat /etc/nginx/nginx.conf
```

One-line:

```bash
docker exec 82197eccd1ff cat /etc/nginx/nginx.conf
```

### Xem cac file site hoac conf include

```bash
docker exec 82197eccd1ff ls -la /etc/nginx
docker exec 82197eccd1ff ls -la /etc/nginx/conf.d
docker exec 82197eccd1ff ls -la /etc/nginx/sites-enabled
docker exec 82197eccd1ff ls -la /etc/nginx/sites-available
```

### Xem toan bo config sau khi Nginx expand include

```bash
docker exec 82197eccd1ff nginx -T
```

Y nghia:

- `nginx -T` in ra toan bo config dang duoc Nginx nap
- bao gom file chinh va cac file `include`
- day la lenh nen dung nhat khi can review config that su dang active

Neu muon luu ra file tren host:

```bash
docker exec 82197eccd1ff nginx -T > nginx-full-config.txt
```

## Validate config Nginx

### Validate syntax co ban

```bash
docker exec 82197eccd1ff nginx -t
```

Y nghia:

- kiem tra syntax config co hop le khong
- khong reload
- an toan, nen chay truoc moi lan reload

Neu config dung, thuong thay:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### Validate voi duong dan config cu the

```bash
docker exec 82197eccd1ff nginx -t -c /etc/nginx/nginx.conf
```

## Reload Nginx

### Reload mem, khong restart process chinh

```bash
docker exec 82197eccd1ff nginx -s reload
```

Y nghia:

- Nginx nap lai config
- worker cu xu ly not request dang mo, worker moi dung config moi
- thuong an toan hon restart

### Reload bang signal

```bash
docker exec 82197eccd1ff kill -HUP 1
```

Luu y:

- thuong process `1` trong container la Nginx master
- cach nay phu thuoc image va entrypoint
- uu tien `nginx -s reload` de ro rang hon

## Quy trinh dung

### 1. Xem full config hien tai

```bash
docker exec 82197eccd1ff nginx -T
```

### 2. Validate truoc khi reload

```bash
docker exec 82197eccd1ff nginx -t
```

### 3. Reload sau khi config hop le

```bash
docker exec 82197eccd1ff nginx -s reload
```

## Bo lenh nen dung nhat

```bash
docker exec 82197eccd1ff nginx -T
docker exec 82197eccd1ff nginx -t
docker exec 82197eccd1ff nginx -s reload
```

## Doc log Nginx

### Xem nhanh log container Nginx

```bash
docker logs 82197eccd1ff
docker logs -f 82197eccd1ff
docker logs --tail 100 82197eccd1ff
docker logs --since 10m 82197eccd1ff
docker logs -t --tail 200 82197eccd1ff
```

### Lay N dong cuoi de soi nhanh

```bash
docker logs --tail 50 82197eccd1ff
docker logs --tail 200 82197eccd1ff
docker logs --tail 500 82197eccd1ff
```

### Loc request theo path hoac status

```bash
docker logs --tail 200 82197eccd1ff 2>&1 | grep '/portal/'
docker logs --tail 200 82197eccd1ff 2>&1 | grep 'HTTP/' | grep -E '" 5[0-9]{2} '
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'HTTP/' | grep -Ev '" 2[0-9]{2} '
```

### Kiem tra request nhanh hay cham

Lenh nay chi hieu qua neu `log_format` co ghi `request_time` hoac `upstream_response_time`.

Vi du log format can co:

```nginx
rt=$request_time urt=$upstream_response_time
```

Neu da co truong do, loc request cham:

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'rt='
docker logs --since 30m 82197eccd1ff 2>&1 | grep -E 'rt=([1-9][0-9]*|[1-9]\.[0-9]+)'
docker logs --since 30m 82197eccd1ff 2>&1 | grep -E 'rt=([2-9]\.[0-9]+|[1-9][0-9]+)'
```

Y nghia:

- `rt=`: tong thoi gian request tai Nginx
- `urt=`: thoi gian upstream phan hoi
- `rt >= 1s`: request cham
- `rt >= 2s`: request rat cham

### Sort de biet request nao cham nhat

Neu log co truong `rt=...`:

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'rt=' | sort -t= -k2,2n
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'rt=' | sort -t= -k2,2n | tail -n 20
```

Canh bao:

- Cach sort nay chi on neu truong `rt=` la cap `key=value` dau tien co so thuc gan nhu trong log format vi du `rt=0.123`
- Neu log format phuc tap hon, nen dung `awk` de tach chinh xac

### Dung `awk` de sort chinh xac theo `rt`

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
```

Lenh nay:

- tach gia tri `rt=...`
- them no vao dau dong
- sort tang dan theo so
- lay 20 request cham nhat

### Soi request cham theo path

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/portal/' | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/api/vnpay/' | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
```

### Xem log theo moc thoi gian gan day

```bash
docker logs --since 5m 82197eccd1ff
docker logs --since 1h 82197eccd1ff
docker logs --since 2026-04-24T09:00:00 82197eccd1ff
```

### Vua follow vua loc request cham

```bash
docker logs -f 82197eccd1ff 2>&1 | grep 'rt='
docker logs -f 82197eccd1ff 2>&1 | grep -E 'rt=([1-9][0-9]*|[1-9]\.[0-9]+)'
```

### Mau lenh thuc dung

```bash
docker logs --tail 200 82197eccd1ff 2>&1 | grep '/portal/'
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'HTTP/' | grep -E '" 5[0-9]{2} '
docker logs --since 30m 82197eccd1ff 2>&1 | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/api/' | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
```

## Ghi chu thuc te

- `nginx -T` dung de xem config that su dang active
- `nginx -t` dung de validate syntax
- `nginx -s reload` dung de ap config moi ma khong can restart container
- Neu container khong co binary `nginx` trong `PATH`, co the can duong dan day du, vi du `/usr/sbin/nginx`
