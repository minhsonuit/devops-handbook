# Capacity Planning

> Ngay bat dau: ___

## Framework

```
1. MEASURE  — Do current usage
2. PREDICT  — Du bao growth
3. PLAN     — Xac dinh khi nao can scale
4. ACT      — Scale truoc khi can
```

## Do current usage

```bash
# CPU trend
docker stats --no-stream --format "{{.Name}}: CPU={{.CPUPerc}} MEM={{.MemUsage}}"

# Disk growth
df -h /var/lib/docker
du -sh /var/lib/docker/volumes/*
```

```promql
# CPU usage trung binh 7 ngay
avg_over_time(
  (100 - avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)[7d:1h]
)

# Disk usage trend
predict_linear(node_filesystem_avail_bytes[7d], 30*24*3600)
# → Du doan disk con bao nhieu sau 30 ngay
```

## Capacity rules of thumb

| Resource | Warning | Critical | Action |
|----------|---------|----------|--------|
| CPU | > 70% avg | > 85% avg | Scale up/out |
| Memory | > 80% | > 90% | Scale up, check leak |
| Disk | > 75% | > 85% | Expand, cleanup |
| Connections | > 70% max | > 85% max | Tang max hoac pool |

## Scaling decisions

```
Vertical scaling (scale up):
  Tang CPU, RAM cua 1 may
  + Don gian
  - Co gioi han vat ly
  → Dung cho: database, stateful services

Horizontal scaling (scale out):
  Them nhieu may
  + Khong gioi han
  - Can load balancer, stateless app
  → Dung cho: web servers, API, workers
```

## Du bao don gian

```
Current: 1000 req/s, CPU 40%
Growth: 20%/thang

Thang 1: 1200 req/s → CPU ~48%
Thang 2: 1440 req/s → CPU ~58%
Thang 3: 1728 req/s → CPU ~69% ⚠️ Warning
Thang 4: 2074 req/s → CPU ~83% 🔴 Critical

→ Can scale truoc thang 3
```
