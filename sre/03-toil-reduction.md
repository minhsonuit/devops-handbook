# Toil Reduction

> Ngay bat dau: ___

## Toil la gi

Cong viec **thu cong, lap di lap lai, co the tu dong hoa, khong tao gia tri lau dai**.

## Vi du toil vs engineering

| Toil ❌ | Engineering ✅ |
|---------|---------------|
| SSH vao server restart service | Tao auto-restart policy |
| Copy file config bang tay | Dung config management |
| Xem log bang mat de tim loi | Tao alert rules |
| Manually scale khi traffic cao | Setup HPA auto-scaling |
| Chay backup script bang tay | Cron job + monitoring |
| Manual deploy | CI/CD pipeline |

## Nguyen tac

- **Muc tieu:** < 50% thoi gian la toil (Google SRE)
- **Tu dong hoa progressive:** bat dau tu cai lap nhieu nhat
- **Document truoc, automate sau:** ghi lai steps → script → pipeline

## Playbook → Script → Automation

```
Level 0: Khong co doc → lam theo kinh nghiem
Level 1: Playbook    → doc step-by-step, lam bang tay
Level 2: Script      → chay script, con can nguoi trigger
Level 3: Automation  → tu dong, chi can monitor
Level 4: Self-healing → tu detect va fix, khong can nguoi
```

## Vi du thuc te

### Truoc: Manual deploy

```
1. SSH vao server
2. docker pull image
3. docker compose down
4. docker compose up -d
5. Kiem tra logs
6. Test URL
```

### Sau: Automated

```bash
#!/bin/bash
set -euo pipefail
TAG="${1:?Usage: deploy.sh <tag>}"
docker pull "myregistry/api:$TAG"
docker compose down
TAG=$TAG docker compose up -d
sleep 10
STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:5001/health)
if [ "$STATUS" = "200" ]; then
    echo "✅ Deploy OK: $TAG"
else
    echo "❌ Deploy FAILED, rolling back"
    TAG=previous docker compose up -d
    exit 1
fi
```

## Tracking toil

| Tuan | Toil (hours) | Engineering (hours) | % Toil |
|------|-------------|-------------------|--------|
| W1 | 20 | 20 | 50% |
| W2 | 15 | 25 | 37% |
| W3 | 10 | 30 | 25% ✅ |
