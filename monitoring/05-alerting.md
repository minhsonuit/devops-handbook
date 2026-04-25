# Alerting

> Ngay bat dau: ___

## Nguyen tac alerting

1. **Alert actionable** — chi alert khi can hanh dong
2. **Khong alert qua nhieu** — alert fatigue se khien ignore tat ca
3. **Co severity** — Critical, Warning, Info
4. **Co runbook** — moi alert co huong dan xu ly

## Alertmanager (Prometheus)

```yaml
# alertmanager.yml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alert@company.com'
  smtp_auth_username: 'alert@company.com'
  smtp_auth_password: 'YOUR_APP_PASSWORD'

route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'email'

  routes:
    - match:
        severity: critical
      receiver: 'slack-critical'

receivers:
  - name: 'email'
    email_configs:
      - to: 'devops@company.com'

  - name: 'slack-critical'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL'
        channel: '#alerts-critical'
```

## Alert rules (Prometheus)

```yaml
# alert-rules.yml
groups:
  - name: system
    rules:
      - alert: HighCPU
        expr: 100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "CPU cao tren {{ $labels.instance }}"

      - alert: DiskAlmostFull
        expr: (1 - node_filesystem_avail_bytes / node_filesystem_size_bytes) * 100 > 85
        for: 10m
        labels:
          severity: critical
        annotations:
          summary: "Disk sap day tren {{ $labels.instance }}"

      - alert: ContainerDown
        expr: absent(container_last_seen{name="api"})
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Container api khong hoat dong"
```

## Alerts nen co

| Alert | Muc | Threshold |
|-------|-----|-----------|
| CPU cao | Warning | > 80% trong 5m |
| RAM cao | Warning | > 85% trong 5m |
| Disk day | Critical | > 85% |
| Container restart | Warning | > 3 lan/1h |
| Error rate cao | Critical | > 5% trong 5m |
| Response time cao | Warning | p95 > 2s trong 5m |
| SSL cert sap het han | Warning | < 14 ngay |
