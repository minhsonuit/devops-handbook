# Performance Troubleshooting

> Ngay bat dau: ___

## CPU cao

```bash
# Tim process ngon CPU
top -bn1 | head -20
ps aux --sort=-%cpu | head -10

# Trong Docker
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

## Memory cao / leak

```bash
# Tong quan
free -h

# Process dung nhieu RAM
ps aux --sort=-%mem | head -10

# Docker container
docker stats --no-stream

# Kiem tra OOM
dmesg | grep -i "out of memory"
docker inspect CONTAINER --format='{{.State.OOMKilled}}'
```

## Disk I/O cham

```bash
# Xem I/O
iostat -x 1 5
iotop

# Tim file lon
find / -type f -size +100M 2>/dev/null | head -20
du -sh /var/log/* | sort -h | tail -10

# Docker
docker system df
```

## Request cham

```bash
# Nginx
docker logs --since 30m NGINX 2>&1 | awk 'match($0,/rt=([0-9.]+)/,a){print a[1], $0}' | sort -n | tail -10

# Kiem tra backend
docker exec NGINX sh -c "curl -w 'time: %{time_total}s\n' -o /dev/null -s http://app:5001/health"
```

## Checklist performance

- [ ] CPU < 80%
- [ ] RAM < 85%
- [ ] Disk < 85%
- [ ] Response time p95 < 2s
- [ ] Error rate < 1%
- [ ] No OOM kills
- [ ] No container restarts
