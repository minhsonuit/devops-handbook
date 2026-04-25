# Architecture Anti-Patterns — Thiet ke tuong tot nhung gay hoa

> "Toi uu hoa som la goc re cua moi toi loi." — Donald Knuth

---

## 1. Premature Microservices

### Lam tuong
"Microservices la best practice, nen bat dau voi microservices."

### Thuc te
Microservices them complexity khong lo:
- Network latency giua services
- Distributed transactions
- Data consistency across services
- Debugging across services
- Deploy coordination
- Monitoring complexity x10

### Nguyen tac
```
1 developer  → Monolith
1 team       → Modular monolith
5+ teams     → Microservices (co the)

Ban dang co 1 van de → tao 10 microservices → gio ban co 11 van de
```

### Khi nao moi can microservices
- Team >= 5-10 developers
- Can scale tung phan doc lap
- Services co lifecycle khac nhau
- Team ownership ro rang

---

## 2. Shared Database — "Tien loi"

### Lam tuong
"2 services dung chung 1 database de de chia se data."

### Thuc te
```
Service A ──→ [Database] ←── Service B
                  │
          Thay doi schema?
          → Ca 2 service deu phai update
          → Deploy phai dong bo
          → Tight coupling → mat loi ich cua services rieng
```

### Dung cach
```
Service A → DB_A ──→ Event ──→ Service B → DB_B
         (owns data)        (reads copy)
```

Moi service owns data cua no. Chia se qua API hoac events.

---

## 3. Distributed Monolith — "Worst of both worlds"

### Lam tuong
"Tach thanh microservices = linh hoat."

### Thuc te
Neu deploy 1 service bat buoc phai deploy cac service khac → **distributed monolith**:
- Co tat ca complexity cua microservices
- Khong co loi ich nao cua microservices
- Cham hon monolith (network overhead)

### Dau hieu nhan biet
- Deploy 1 service → phai deploy 3 service khac
- Service A goi Service B goi Service C goi Service A (circular dependency)
- Shared models/DTOs giua services
- 1 team quan ly tat ca services

---

## 4. "Let's add a cache" — Cache la dau truong

### Lam tuong
"Query cham? Them Redis cache, xong."

### Thuc te — 2 hard problems in CS: cache invalidation va naming things

```
Race condition:
1. User A doc cache  → gia tri 100
2. User B update DB  → gia tri 200
3. User B xoa cache
4. User A ghi cache  → gia tri 100 (stale!)
```

### Loi pho bien voi cache
| Loi | Hau qua |
|-----|---------|
| TTL qua dai | Data stale, user thay du lieu cu |
| TTL qua ngan | Cache ko hieu qua, DB van bi hit nhieu |
| Cache stampede | Cache het han → 1000 request cung hit DB |
| Cache khong invalidate | Update DB nhung cache van tra du lieu cu |
| Cache qua nhieu | Redis het RAM → eviction khong mong muon |

### Nguyen tac
- Cache chi nen la **optimization**, khong phai **requirement**
- He thong phai **chay duoc khi khong co cache** (chi cham hon)
- Luon co strategy invalidation ro rang
- Monitor cache hit ratio — duoi 80% thi xem lai

---

## 5. Over-engineering monitoring

### Lam tuong
"Collect moi metric, log moi thu, trace moi request."

### Thuc te
- 500GB log/ngay → khong ai doc
- 10,000 metrics → khong ai xem
- Full tracing → tang latency 20%
- Chi phi storage/compute cho monitoring > chi phi app

### Dung cach
```
Start simple:
1. 4 Golden Signals (latency, traffic, errors, saturation)
2. Health check endpoint
3. Error logs (khong phai debug logs)
4. 5 alerts co action plan

Scale monitoring khi can, khong phai tu dau.
```

---

## 6. "One tool to rule them all"

### Lam tuong
"Dung K8s cho moi thu, ke ca cron job 1 dong."

### Thuc te
| Task | Dung | Overkill |
|------|------|---------|
| Cron job don gian | crontab, systemd timer | K8s CronJob |
| Static website | S3 + CDN | K8s Deployment |
| 1 container app | docker compose | K8s cluster |
| MVP | Heroku, App Service | Terraform + K8s + ArgoCD |

### Nguyen tac
**Chon tool theo van de, khong phai theo trend.**

---

## 7. Retry without backoff — "DDoS chinh minh"

### Lam tuong
"Service B loi? Retry ngay, retry nhieu lan."

### Thuc te
```
Service A → Service B (dang qua tai)
         → Retry 1 (them tai)
         → Retry 2 (them tai)
         → Retry 3 (them tai)
         × 1000 instances
         = DDoS Service B
         = Cascade failure
```

### Dung cach
```csharp
// Exponential backoff + jitter
.WaitAndRetryAsync(3, retryAttempt =>
    TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))
    + TimeSpan.FromMilliseconds(Random.Shared.Next(0, 1000)));

// Circuit breaker — dung retry khi service dang down
.CircuitBreakerAsync(
    handledEventsAllowedBeforeBreaking: 5,
    durationOfBreak: TimeSpan.FromSeconds(30));
```
