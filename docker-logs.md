# Docker Logs va Debug

## Xem log container

```bash
docker logs web
docker logs -f web
docker logs --tail 100 web
docker logs --since 10m web
docker logs -f --tail 200 web
docker logs -t web
```

## Xem log voi Docker Compose

```bash
docker compose logs
docker compose logs -f
docker compose logs app
docker compose logs --tail 100 -f app
```

## Xem log voi Docker Swarm

```bash
docker service logs mystack_api
docker service logs -f mystack_api
docker service logs --tail 100 mystack_api
docker service logs --since 30m mystack_api
```

## Ky thuat loc log

Nguyen tac chon lenh:

- Uu tien `--tail` va `--since` truoc khi loc
- `grep` co gioi han pham vi la `nhe` hon quet toan bo log
- `jq` manh hon nhung thuong `nang` hon `grep`
- `docker service logs` tren service nhieu replica co the `nang` hon `docker logs`

### Theo tu khoa

```bash
docker logs web 2>&1 | grep -i error
docker compose logs app 2>&1 | grep -i timeout
docker service logs mystack_api 2>&1 | grep -i exception
```

Giai thich nhanh:

- `2>&1`: gop `stderr` vao `stdout` de `grep` loc duoc ca 2 luong log
- `grep -i`: tim khong phan biet chu hoa chu thuong

### Theo nhieu tu khoa

```bash
docker logs web 2>&1 | grep -Ei 'error|timeout|failed'
docker compose logs app 2>&1 | grep -Ei 'panic|fatal|exception'
docker service logs mystack_api 2>&1 | grep -Ei 'timeout|refused|unhealthy'
```

Giai thich nhanh:

- `grep -E`: bat extended regex
- Dau `|`: nghia la `hoac`

### Theo keyword va follow realtime

```bash
docker logs -f web 2>&1 | grep -i error
docker compose logs -f app 2>&1 | grep -i timeout
docker service logs -f mystack_api 2>&1 | grep -i exception
```

Lenh nay dung khi ban muon chi nhin cac dong moi xuat hien co chua keyword.

### Theo keyword va chi lay log gan day

```bash
docker logs --since 10m web 2>&1 | grep -i error
docker compose logs --tail 200 app 2>&1 | grep -i timeout
docker service logs --since 30m mystack_api 2>&1 | grep -i failed
```

### Loai bo keyword khong muon thay

```bash
docker logs web 2>&1 | grep -iv healthcheck
docker compose logs app 2>&1 | grep -iv '/health'
docker service logs mystack_api 2>&1 | grep -iv 'probe succeeded'
```

Giai thich nhanh:

- `grep -v`: loai bo dong khop mau
- `grep -iv`: vua bo qua hoa thuong, vua loai bo dong khop

### Loc log co nhieu dieu kien

```bash
docker logs web 2>&1 | grep -i error | grep -i mysql
docker compose logs app 2>&1 | grep -i timeout | grep -i redis
docker service logs mystack_api 2>&1 | grep -i failed | grep -i payment
```

Lenh nay dung khi ban muon tim dong log co dong thoi nhieu dieu kien.

### Hien context quanh dong khop

```bash
docker logs web 2>&1 | grep -iC 3 error
docker compose logs app 2>&1 | grep -iC 2 timeout
docker service logs mystack_api 2>&1 | grep -iC 5 exception
```

Giai thich nhanh:

- `-C 3`: hien them 3 dong truoc va 3 dong sau dong khop

### Dem so dong khop keyword

```bash
docker logs web 2>&1 | grep -ic error
docker compose logs app 2>&1 | grep -ic timeout
docker service logs mystack_api 2>&1 | grep -ic exception
```

Giai thich nhanh:

- `-c`: dem so dong khop thay vi in tung dong

### Chi lay phan log khop dau tien

```bash
docker logs web 2>&1 | grep -im 1 error
docker compose logs app 2>&1 | grep -im 1 timeout
docker service logs mystack_api 2>&1 | grep -im 1 exception
```

Giai thich nhanh:

- `-m 1`: dung sau khi gap 1 ket qua khop

### Theo moc thoi gian

```bash
docker logs --since 2026-04-01T10:00:00 web
docker logs --since 30m web
```

### Chi lay dong cuoi

```bash
docker logs --tail 200 web
docker compose logs --tail 200 app
docker service logs --tail 200 mystack_api
```

### Log JSON

Neu app xuat log JSON:

```bash
docker logs web 2>&1 | jq
docker logs web 2>&1 | jq '.level, .message'
docker logs web 2>&1 | jq 'select(.level == "error")'
docker service logs mystack_api 2>&1 | jq 'select(.message | test("timeout"; "i"))'
```

## Khi khong thay log

Nguyen nhan thuong gap:

- Ung dung ghi log vao file trong container thay vi `stdout/stderr`
- Container vua restart qua nhanh
- Dang xem sai service hoac sai container
- Logging driver khac mac dinh

Lenh kiem tra:

```bash
docker inspect <container>
docker exec -it <container> sh
```

## Debug thuc chien

### Container crash

```bash
docker ps -a
docker logs <container>
docker inspect <container> --format='{{.State.ExitCode}}'
```

### Kiem tra process treo

```bash
docker top <container>
docker stats
```

### Kiem tra mang

```bash
docker exec -it <container> ping redis
docker exec -it <container> nslookup redis
```

### Kiem tra volume va file log ben trong

```bash
docker inspect <container>
docker exec -it <container> sh
```

## Best practices

- Ghi log ra `stdout/stderr`.
- Dung structured logging neu can parse tu dong.
- Gan timestamp cho log.
- Khong phu thuoc hoan toan vao `docker logs` trong production.
- Neu production lon, dung ELK, Loki, Graylog hoac Datadog de tap trung log.
