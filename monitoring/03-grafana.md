# Grafana

> Ngay bat dau: ___

## Grafana la gi

- Visualization platform cho metrics va logs
- Ket noi nhieu data sources: Prometheus, Elasticsearch, PostgreSQL, ...
- Dashboard, alerting, annotations

## Cai dat bang Docker

```yaml
services:
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=YOUR_PASSWORD
    volumes:
      - grafana_data:/var/lib/grafana
    restart: unless-stopped

volumes:
  grafana_data:
```

Truy cap: http://localhost:3000 (admin / YOUR_PASSWORD)

## Them data source

1. Menu → Connections → Data Sources → Add
2. Chon Prometheus
3. URL: `http://prometheus:9090` (ten service trong Docker)
4. Save & Test

## Dashboard pho bien (import bang ID)

| Dashboard | Grafana ID | Dung cho |
|-----------|------------|----------|
| Node Exporter Full | 1860 | Linux system metrics |
| Docker Dashboard | 893 | Docker containers |
| Nginx | 12708 | Nginx stats |
| PostgreSQL | 9628 | PostgreSQL |
| Redis | 11835 | Redis |

Import: Dashboards → Import → nhap ID → Load → Select data source → Import

## Tao panel co ban

1. Dashboard → Add Panel
2. Chon Data Source: Prometheus
3. Nhap PromQL query
4. Chon visualization type (Time Series, Gauge, Stat, Table)
5. Dat title, legend, unit
6. Apply

## Alerting

1. Dashboard → Panel → Edit → Alert tab
2. Dat condition: `WHEN avg() OF query IS ABOVE 80`
3. Chon notification channel (Email, Slack, PagerDuty)

## Backup Grafana

```bash
# Backup database
docker cp grafana:/var/lib/grafana/grafana.db ./grafana.db.backup

# Export dashboards bang API
curl -s http://admin:password@localhost:3000/api/dashboards/uid/DASHBOARD_UID | jq > dashboard.json
```
