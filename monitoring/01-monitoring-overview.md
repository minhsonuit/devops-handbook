# Monitoring Overview

> Ngay bat dau: ___

## 3 Pillars of Observability

```
1. Metrics   — So lieu (CPU, RAM, request count, latency)
               Tool: Prometheus, Grafana
2. Logs      — Su kien chi tiet (error messages, access logs)
               Tool: ELK Stack, Loki, Fluentd
3. Traces    — Luong request qua nhieu services
               Tool: Jaeger, Zipkin, OpenTelemetry
```

## Metrics vs Logs vs Traces

| | Metrics | Logs | Traces |
|---|---------|------|--------|
| Dang | So | Text | DAG (graph) |
| Volume | Thap | Cao | Trung binh |
| Tra cuu | Nhanh | Cham | Trung binh |
| Dung khi | Dashboard, alert | Debug chi tiet | Debug across services |

## Monitoring stack pho bien

```
App → Prometheus (scrape metrics) → Grafana (visualize)
App → Fluentd/Filebeat (ship logs) → Elasticsearch → Kibana (search)
App → OpenTelemetry (traces) → Jaeger (visualize)
```

## Nhung gi can monitor

### Infrastructure
- CPU, RAM, Disk, Network I/O
- Container health, restart count
- Docker/K8s resource usage

### Application
- Request rate (requests/sec)
- Error rate (% of 5xx)
- Latency (p50, p95, p99)
- Queue depth, processing time

### Business
- Orders/min, revenue
- User signups, active sessions
- Payment success rate

## RED Method (cho services)

- **R**ate — request/sec
- **E**rrors — error rate
- **D**uration — latency

## USE Method (cho resources)

- **U**tilization — % dang dung
- **S**aturation — queue length
- **E**rrors — error count

## Golden Signals (Google SRE)

1. Latency
2. Traffic
3. Errors
4. Saturation
