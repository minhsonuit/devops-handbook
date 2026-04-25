# SLO / SLI / SLA

> Ngay bat dau: ___

## Dinh nghia

```
SLI (Service Level Indicator) — SO DO THUC TE
  "99.2% requests tra lai trong < 500ms"

SLO (Service Level Objective) — MUC TIEU NOI BO
  "99.5% requests phai tra lai trong < 500ms"

SLA (Service Level Agreement) — HOP DONG VOI KHACH HANG
  "99.0% uptime, neu vi pham → boi thuong"

Quan he: SLA < SLO < 100%
                ↑
              SLI do luong
```

## SLI cho web services

| SLI | Do luong | Nguon |
|-----|----------|-------|
| Availability | % requests thanh cong (non-5xx) | Nginx access log, Prometheus |
| Latency | p50, p95, p99 response time | Nginx rt=, OTel metrics |
| Error rate | % requests tra 5xx | Nginx log, app metrics |
| Throughput | Requests per second | Prometheus counter |

## SLO examples

| Service | SLO | Measurement window |
|---------|-----|-------------------|
| API POS | 99.9% availability | 30 days rolling |
| API POS | p99 latency < 1s | 30 days rolling |
| Payment API | 99.95% availability | 30 days rolling |
| Background jobs | 99% completion rate | 7 days rolling |

## Tinh SLI tu Prometheus

```promql
# Availability (% non-5xx)
sum(rate(http_requests_total{status!~"5.."}[30d]))
/
sum(rate(http_requests_total[30d]))

# Latency SLI (% requests < 500ms)
sum(rate(http_request_duration_seconds_bucket{le="0.5"}[30d]))
/
sum(rate(http_request_duration_seconds_count[30d]))
```

## Tinh tu Nginx logs

```bash
# Availability
TOTAL=$(docker logs --since 720h NGINX 2>&1 | grep 'HTTP/' | wc -l)
ERRORS=$(docker logs --since 720h NGINX 2>&1 | grep 'HTTP/' | grep -cE '" 5[0-9]{2} ')
echo "scale=4; ($TOTAL - $ERRORS) / $TOTAL * 100" | bc
# → 99.82%

# p99 latency
docker logs --since 720h NGINX 2>&1 | \
  awk 'match($0,/rt=([0-9.]+)/,a){print a[1]}' | \
  sort -n | \
  awk 'BEGIN{c=0} {a[c++]=$1} END{print "p99: " a[int(c*0.99)] "s"}'
```

## Budget table

| SLO | Downtime/month | Downtime/year |
|-----|---------------|---------------|
| 99% | 7h 18m | 3.65 days |
| 99.5% | 3h 39m | 1.83 days |
| 99.9% | 43m 50s | 8.77 hours |
| 99.95% | 21m 55s | 4.38 hours |
| 99.99% | 4m 23s | 52.6 minutes |
