# Nginx Monitoring and Logs

## Nguon du lieu de monitor

- `stub_status`
- access log
- error log
- `docker logs`
- `docker stats`
- socket state bang `ss`

## Kiem tra connection hien tai

Neu da bat `stub_status`:

```bash
curl -s http://127.0.0.1/nginx_status
docker exec 82197eccd1ff curl -s http://127.0.0.1/nginx_status
```

Cac truong can nhin:

- `Active connections`
- `Reading`
- `Writing`
- `Waiting`

## Doc log nhanh

```bash
docker logs --tail 100 82197eccd1ff
docker logs -f --tail 100 82197eccd1ff
docker logs --since 30m 82197eccd1ff
docker logs -t --tail 200 82197eccd1ff
```

## Loc request loi

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'HTTP/' | grep -E '" 5[0-9]{2} '
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'HTTP/' | grep -Ev '" 2[0-9]{2} '
```

## Loc theo path

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/portal/'
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/webhook/'
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/api/vnpay/'
```

## Soi request cham

Nen them vao `log_format`:

```nginx
rt=$request_time urt=$upstream_response_time
```

Sau do:

```bash
docker logs --since 30m 82197eccd1ff 2>&1 | grep 'rt='
docker logs --since 30m 82197eccd1ff 2>&1 | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
docker logs --since 30m 82197eccd1ff 2>&1 | grep '/portal/' | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -n 20
```

## Cac dau hieu can tim trong error log

```bash
docker logs --since 6h 82197eccd1ff 2>&1 | grep -Ei 'worker_connections|too many open files|accept4\(\) failed|connect\(\) failed|connection timed out|upstream timed out|client prematurely closed connection'
```

## Monitor muc he thong

```bash
docker stats
docker exec 82197eccd1ff ss -tan
docker exec 82197eccd1ff ss -tan state established
```

## Ghi chu thuc dung

- `stub_status` la snapshot thoi diem hien tai
- access log cho biet request nào, status gi, cham hay nhanh
- error log cho biet ly do ky thuat
- neu can monitor nghiem tuc, nen day log va metric vao he thong tap trung
