# Prometheus

> Ngay bat dau: ___

## Prometheus la gi

- Time-series database cho metrics
- Pull model: Prometheus chu dong scrape metrics tu targets
- Ngon ngu query: PromQL
- Tu tich hop alerting

## Kien truc

```
App (expose /metrics) ←── Prometheus (scrape) → Alertmanager → Email/Slack
                                    ↓
                               Grafana (visualize)
```

## Cai dat bang Docker

```yaml
# docker-compose.yml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped

volumes:
  prometheus_data:
```

## Config co ban (prometheus.yml)

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "nginx"
    static_configs:
      - targets: ["nginx-exporter:9113"]

  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100"]

  - job_name: "dotnet-app"
    metrics_path: /metrics
    static_configs:
      - targets: ["api:5001"]
```

## PromQL co ban

```promql
# Request rate 5 phut
rate(http_requests_total[5m])

# Error rate
rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m])

# P95 latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# CPU usage
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory usage %
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100

# Disk usage %
(1 - node_filesystem_avail_bytes / node_filesystem_size_bytes) * 100
```

## Exporters pho bien

| Exporter | Port | Metrics |
|----------|------|---------|
| node-exporter | 9100 | CPU, RAM, disk, network |
| nginx-exporter | 9113 | Nginx connections, requests |
| postgres-exporter | 9187 | PostgreSQL stats |
| redis-exporter | 9121 | Redis memory, commands |
| sql-server-exporter | 4000 | SQL Server stats |

## .NET app expose metrics

```csharp
// NuGet: prometheus-net.AspNetCore
app.UseMetricServer();    // Expose /metrics endpoint
app.UseHttpMetrics();     // Auto track HTTP metrics
```
