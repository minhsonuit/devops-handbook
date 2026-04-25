# Automation Recipes

> Ngay bat dau: ___

## Monitor disk va alert

```bash
#!/bin/bash
THRESHOLD=85
DISK_USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')

if [ "$DISK_USAGE" -gt "$THRESHOLD" ]; then
    echo "⚠️ Disk usage: ${DISK_USAGE}% (threshold: ${THRESHOLD}%)" | \
    mail -s "ALERT: Disk almost full on $(hostname)" devops@company.com
fi
```

## Auto restart unhealthy container

```bash
#!/bin/bash
CONTAINERS=("api" "nginx" "notify")

for name in "${CONTAINERS[@]}"; do
    health=$(docker inspect --format='{{.State.Health.Status}}' "$name" 2>/dev/null || echo "not_found")
    if [ "$health" = "unhealthy" ]; then
        echo "$(date): Restarting unhealthy container: $name"
        docker restart "$name"
    fi
done
```

## SSL cert expiry check

```bash
#!/bin/bash
DOMAINS=("api-pos.pharmacity.vn" "portal.pharmacity.vn")
WARN_DAYS=14

for domain in "${DOMAINS[@]}"; do
    expiry=$(echo | openssl s_client -connect "$domain:443" -servername "$domain" 2>/dev/null | \
             openssl x509 -noout -enddate 2>/dev/null | cut -d= -f2)
    if [ -n "$expiry" ]; then
        expiry_epoch=$(date -d "$expiry" +%s 2>/dev/null || date -j -f "%b %d %T %Y %Z" "$expiry" +%s 2>/dev/null)
        now_epoch=$(date +%s)
        days_left=$(( (expiry_epoch - now_epoch) / 86400 ))
        if [ "$days_left" -lt "$WARN_DAYS" ]; then
            echo "⚠️ $domain: $days_left ngay con lai!"
        else
            echo "✅ $domain: $days_left ngay con lai"
        fi
    fi
done
```

## Cron examples

```bash
# Edit crontab
crontab -e

# Syntax: MIN HOUR DAY MONTH WEEKDAY COMMAND
# Health check moi 5 phut
*/5 * * * * /opt/scripts/health-check.sh >> /var/log/health.log 2>&1

# Backup moi ngay 3h sang
0 3 * * * /opt/scripts/backup.sh >> /var/log/backup.log 2>&1

# Disk check moi gio
0 * * * * /opt/scripts/disk-check.sh

# SSL check moi ngay
0 9 * * * /opt/scripts/ssl-check.sh
```
