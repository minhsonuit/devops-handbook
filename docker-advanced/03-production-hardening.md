# Docker Production Hardening

> Ngay bat dau: ___

## Checklist production

### Security

```yaml
services:
  api:
    read_only: true                   # Filesystem read-only
    tmpfs:
      - /tmp                          # Chi cho ghi /tmp
    security_opt:
      - no-new-privileges:true        # Khong cho privilege escalation
    cap_drop:
      - ALL                           # Drop tat ca Linux capabilities
    cap_add:
      - NET_BIND_SERVICE              # Chi add nhung gi can
    user: "1000:1000"                 # Khong chay root
```

### Resource limits

```yaml
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 128M
    # Khi container vuot memory limit → OOM Kill
    # Khi container vuot CPU limit → throttle (cham lai)
```

### Restart policy

```yaml
    restart: unless-stopped
    # Hoac trong Swarm/K8s:
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
```

### Logging

```yaml
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
        tag: "{{.Name}}"
```

### Health checks

```yaml
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:5001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

## Docker daemon hardening

```json
// /etc/docker/daemon.json
{
  "live-restore": true,
  "userland-proxy": false,
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "default-ulimits": {
    "nofile": { "Name": "nofile", "Hard": 65535, "Soft": 65535 }
  },
  "storage-driver": "overlay2"
}
```

## Image scanning

```bash
# Docker Scout (built-in)
docker scout cves myapp:latest
docker scout quickview myapp:latest
docker scout recommendations myapp:latest

# Trivy (open source)
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image myapp:latest
```

## Cleanup tự động

```bash
# Cron job: chay hang ngay
0 2 * * * docker system prune -f --filter "until=168h"
0 2 * * 0 docker image prune -a -f --filter "until=168h"
```
