# Bash Patterns

> Ngay bat dau: ___

## Health check script

```bash
#!/bin/bash
set -euo pipefail

SERVICES=(
    "http://localhost:5001/health|API"
    "http://localhost:80/nginx_status|Nginx"
    "http://localhost:9090/-/healthy|Prometheus"
)

for entry in "${SERVICES[@]}"; do
    IFS='|' read -r url name <<< "$entry"
    status=$(curl -s -o /dev/null -w "%{http_code}" --max-time 5 "$url" 2>/dev/null || echo "000")
    if [ "$status" = "200" ]; then
        echo "✅ $name: OK"
    else
        echo "❌ $name: HTTP $status"
    fi
done
```

## Log rotate script

```bash
#!/bin/bash
LOG_DIR="/var/log/myapp"
MAX_DAYS=30

find "$LOG_DIR" -name "*.log" -mtime +$MAX_DAYS -delete
echo "$(date): Cleaned logs older than $MAX_DAYS days"
```

## Backup script

```bash
#!/bin/bash
set -euo pipefail

BACKUP_DIR="/backup/$(date +%Y-%m-%d)"
mkdir -p "$BACKUP_DIR"

# Backup database
docker exec postgres pg_dump -U postgres mydb > "$BACKUP_DIR/db.sql"

# Backup configs
cp /etc/nginx/nginx.conf "$BACKUP_DIR/"

# Cleanup old backups
find /backup -type d -mtime +7 -exec rm -rf {} + 2>/dev/null

echo "Backup done: $BACKUP_DIR"
```

## Deploy script

```bash
#!/bin/bash
set -euo pipefail

IMAGE="myregistry/api"
TAG="${1:-latest}"

echo "Deploying $IMAGE:$TAG"
docker pull "$IMAGE:$TAG"
docker compose down
docker compose up -d
echo "Waiting for health..."
sleep 10
curl -sf http://localhost:5001/health && echo "✅ Deploy OK" || echo "❌ Deploy FAILED"
```
