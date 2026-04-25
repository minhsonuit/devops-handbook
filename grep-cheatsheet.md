# Grep Cheat Sheet cho Docker Logs

File nay tap trung vao:

- Loc log theo keyword
- Chon lenh theo muc do `nhe`, `vua`, `nang`
- Cac mau lenh an toan khi log rat nhieu

## Cach hieu muc do nang nhe

- `Nhe`: an toan, nen dung truoc. Gioi han log tot, it an CPU va I/O.
- `Vua`: hop ly de debug, nhung van nen gioi han pham vi bang `--tail` hoac `--since`.
- `Nang`: co the rat ton CPU, RAM, I/O hoac ngap output. Chi dung khi that su can.

Luu y:

- Muc do nay danh gia o phia may chay lenh va luong log phai quet.
- `docker service logs` doc luong log cua service. Neu service co nhieu replica va log lon, lenh se nang hon dang ke.
- Hay uu tien gioi han log truoc khi `grep`.

## Nhom lenh nen uu tien

### 1. Xem 100-200 dong cuoi roi loc keyword

Muc do: `Nhe`

```bash
docker logs --tail 200 web 2>&1 | grep -i error
docker compose logs --tail 200 app 2>&1 | grep -i timeout
docker service logs --tail 200 mystack_api 2>&1 | grep -i exception
```

Khi nao dung:

- Muon xem nhanh loi moi nhat
- Service co log nhieu
- Khong muon quet log qua cu

### 2. Gioi han theo thoi gian roi loc

Muc do: `Nhe` den `Vua`

```bash
docker logs --since 10m web 2>&1 | grep -i error
docker compose logs --since 15m app 2>&1 | grep -Ei 'timeout|failed'
docker service logs --since 30m mystack_api 2>&1 | grep -i payment
```

Khi nao dung:

- Loi vua xay ra trong mot khoang thoi gian ro rang
- Muon tranh quet log cu

### 3. Follow realtime va loc keyword

Muc do: `Nhe` neu luong log vua phai, `Vua` neu log den lien tuc

```bash
docker logs -f web 2>&1 | grep -i error
docker compose logs -f app 2>&1 | grep -i timeout
docker service logs -f mystack_api 2>&1 | grep -Ei 'error|exception'
```

Khi nao dung:

- Dang tai hien loi realtime
- Muon chi nhin dong moi

Can nhac:

- Neu service rat on ao, `-f` van co the rat met vi output den lien tuc.
- Nen ket hop them `--tail 100` truoc khi `-f` neu ho tro.

## Nhom lenh debug muc trung binh

### 4. Loc nhieu keyword bang regex

Muc do: `Vua`

```bash
docker logs --tail 500 web 2>&1 | grep -Ei 'error|timeout|refused|failed'
docker service logs --since 30m mystack_api 2>&1 | grep -Ei 'panic|fatal|exception'
```

Khi nao dung:

- Chua biet loi thuoc nhom nao
- Muon quet mot bo keyword thuong gap

Can nhac:

- Regex rong hon se ton hon `grep` don gian.
- Van nen gioi han bang `--tail` hoac `--since`.

### 5. Loc nhieu dieu kien lien tiep

Muc do: `Vua`

```bash
docker logs --tail 500 web 2>&1 | grep -i error | grep -i mysql
docker service logs --since 30m mystack_api 2>&1 | grep -i failed | grep -i payment
```

Khi nao dung:

- Muon tim mot nhom loi cu the hon
- Muon giam bot output

Can nhac:

- Moi lan `grep` la them mot buoc xu ly.
- Van chua qua nang neu luong log dau vao da duoc gioi han.

### 6. Hien context quanh dong khop

Muc do: `Vua`

```bash
docker logs --tail 300 web 2>&1 | grep -iC 3 error
docker service logs --tail 300 mystack_api 2>&1 | grep -iC 5 exception
```

Khi nao dung:

- Can xem dong truoc sau de hieu nguyen nhan

Can nhac:

- Output se phong to nhanh.
- Khong nen dung voi `--tail` qua lon neu service log rat day.

## Nhom lenh nang, dung co kiem soat

### 7. Quet toan bo log service roi loc

Muc do: `Nang`

```bash
docker service logs mystack_api 2>&1 | grep -i error
docker compose logs app 2>&1 | grep -i timeout
docker logs web 2>&1 | grep -i failed
```

Rui ro:

- Quet nhieu du lieu khong can thiet
- Cham, ngap terminal
- Khong phu hop khi log da rat lon

Nen thay bang:

```bash
docker service logs --tail 200 mystack_api 2>&1 | grep -i error
```

### 8. Dem so dong khop tren log rat lon

Muc do: `Vua` den `Nang`

```bash
docker service logs mystack_api 2>&1 | grep -ic error
```

Can nhac:

- Nhe o dau ra, nhung van phai quet het input
- Neu input lon thi van nang

Nen thay bang:

```bash
docker service logs --since 30m mystack_api 2>&1 | grep -ic error
```

### 9. Parse JSON bang `jq`

Muc do: `Vua` den `Nang`

```bash
docker logs --tail 300 web 2>&1 | jq
docker service logs --since 10m mystack_api 2>&1 | jq 'select(.level == "error")'
docker service logs --since 10m mystack_api 2>&1 | jq 'select(.message | test("timeout"; "i"))'
```

Can nhac:

- `jq` rat manh, nhung ton hon `grep`
- Chi dung khi log thuc su la JSON hop le

## Thu tu uu tien khi debug

1. Thu `--tail 100` hoac `--tail 200` truoc
2. Neu biet khoang thoi gian, them `--since`
3. Neu dang canh loi xay ra, dung `-f`
4. Chi khi can moi mo rong sang regex nhieu keyword, context, hoac `jq`
5. Tranh quet toan bo log service lon neu chua can

## Bang quyet dinh nhanh

| Muc tieu | Lenh nen dung | Do nang | Khi nao tranh |
| --- | --- | --- | --- |
| Xem nhanh loi moi nhat | `docker service logs --tail 100 mystack_api 2>&1 \| grep -i error` | Nhe | Khi can full context xa hon 100 dong |
| Tim loi trong 10-30 phut gan day | `docker service logs --since 30m mystack_api 2>&1 \| grep -Ei 'error|timeout|failed'` | Nhe den Vua | Khi khong biet moc thoi gian |
| Theo doi loi realtime | `docker service logs -f --tail 50 mystack_api 2>&1 \| grep -Ei 'error|exception'` | Vua | Khi service qua on ao, log do lien tuc |
| Tim loi theo nhieu keyword | `docker service logs --tail 300 mystack_api 2>&1 \| grep -Ei 'timeout|refused|unhealthy'` | Vua | Khi log rat lon ma chua gioi han pham vi |
| Tim loi rat cu the | `docker service logs --since 30m mystack_api 2>&1 \| grep -i failed \| grep -i payment` | Vua | Khi chua chac keyword phu hop |
| Xem loi kem ngữ cảnh | `docker service logs --tail 300 mystack_api 2>&1 \| grep -iC 4 exception` | Vua | Khi terminal de bi ngap output |
| Bo qua noise nhu healthcheck | `docker service logs --tail 200 mystack_api 2>&1 \| grep -ivE 'healthcheck|/health|probe succeeded'` | Nhe | Khi noise khong phai van de |
| Dem so lan loi xuat hien | `docker service logs --since 30m mystack_api 2>&1 \| grep -ic error` | Vua | Khi can noi dung chi tiet cua tung dong |
| Parse log JSON | `docker service logs --since 10m mystack_api 2>&1 \| jq 'select(.level == "error")'` | Vua den Nang | Khi log khong phai JSON hop le |
| Quet toan bo log service | `docker service logs mystack_api 2>&1 \| grep -i error` | Nang | Gan nhu luon nen tranh neu co the dung `--tail` hoac `--since` |

## Ban rat ngan de nho

- Xem nhanh: `--tail 100`
- Xem theo su co vua xay ra: `--since 10m`
- Canh realtime: `-f --tail 50`
- Loc keyword don gian: `grep -i`
- Loc nhieu keyword: `grep -Ei 'a|b|c'`
- Bo qua noise: `grep -ivE 'health|probe'`
- Dung `jq` chi khi log la JSON
- Tranh quet full log neu service lon

## Mau chon lenh theo tinh huong

### Muon xem nhanh co loi khong

Muc do: `Nhe`

```bash
docker service logs --tail 100 mystack_api 2>&1 | grep -Ei 'error|failed|timeout'
```

### Muon theo doi loi moi phat sinh

Muc do: `Nhe` den `Vua`

```bash
docker service logs -f --tail 50 mystack_api 2>&1 | grep -Ei 'error|exception'
```

### Muon tim loi trong 15 phut vua roi

Muc do: `Nhe`

```bash
docker service logs --since 15m mystack_api 2>&1 | grep -i payment
```

### Muon bo qua healthcheck va noise

Muc do: `Nhe`

```bash
docker service logs --tail 200 mystack_api 2>&1 | grep -ivE 'healthcheck|/health|probe succeeded'
```

### Muon xem loi va ngữ cảnh quanh no

Muc do: `Vua`

```bash
docker service logs --tail 300 mystack_api 2>&1 | grep -iC 4 exception
```

## Quy tac thuc dung

- Luon uu tien `--tail` hoac `--since` truoc khi `grep`
- `grep` don gian nhe hon `grep -E`, va `grep` nhe hon `jq`
- `-f` khong nang luc dau, nhung co the ton neu log chay lien tuc
- Service cang nhieu replica, `docker service logs` cang de phinh output
- Neu can tra cuu sau, nen day log vao he thong tap trung thay vi soi truc tiep qua CLI
