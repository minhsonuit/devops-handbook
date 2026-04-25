# Redis Operations

> Ngay bat dau: ___

## Chay Redis trong Docker

```yaml
services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --requirepass YOUR_PASSWORD --maxmemory 256mb --maxmemory-policy allkeys-lru
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "YOUR_PASSWORD", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

volumes:
  redis_data:
```

## Lenh co ban

```bash
# Ket noi
docker exec -it redis redis-cli -a YOUR_PASSWORD

# Lenh trong redis-cli
PING                          # PONG
INFO                          # Thong tin server
INFO memory                   # Memory usage
INFO stats                    # Statistics
INFO replication              # Replication status
INFO clients                  # Connected clients

DBSIZE                        # So keys
KEYS pattern*                 # Tim keys (KHONG dung production — dung SCAN)
SCAN 0 MATCH pattern* COUNT 100   # Tim keys an toan

# Data operations
GET key
SET key value EX 3600         # Set voi TTL 1h
DEL key
TTL key                       # Thoi gian con lai
EXPIRE key 3600               # Dat TTL

# Monitor realtime (debug only)
MONITOR                       # Xem tat ca commands realtime
```

## Memory management

```bash
# Xem memory
INFO memory
# used_memory_human: bao nhieu RAM dang dung
# maxmemory: gioi han
# maxmemory_policy: chinh sach khi day

# Memory phan tich
MEMORY USAGE key              # Memory cua 1 key
MEMORY DOCTOR                 # Goi y
```

### Eviction policies

| Policy | Mo ta |
|--------|-------|
| `noeviction` | Tra loi loi khi day (default) |
| `allkeys-lru` | Xoa key it dung nhat — **khuyen nghi cho cache** |
| `allkeys-lfu` | Xoa key it dung nhat (frequency) |
| `volatile-lru` | Chi xoa key co TTL |
| `volatile-ttl` | Xoa key sap het han |

## Persistence

```bash
# RDB snapshot (mac dinh)
# Luu snapshot dinh ky vao dump.rdb
BGSAVE                        # Tao snapshot ngay

# AOF (Append Only File) — ben hon, cham hon
# Luu moi write operation
# Cau hinh: appendonly yes
```

```
# redis.conf
save 900 1          # Snapshot sau 900s neu co 1 change
save 300 10         # Snapshot sau 300s neu co 10 changes
save 60 10000       # Snapshot sau 60s neu co 10000 changes

appendonly yes
appendfsync everysec
```

## Backup Redis

```bash
# Copy RDB file
docker cp redis:/data/dump.rdb ./redis_backup_$(date +%Y%m%d).rdb

# Restore: copy file vao va restart
docker cp ./dump.rdb redis:/data/dump.rdb
docker restart redis
```

## Monitoring

```bash
# Slow log
SLOWLOG GET 10                # 10 lenh cham nhat
SLOWLOG LEN                   # So lenh cham
CONFIG SET slowlog-log-slower-than 10000  # Log > 10ms

# Client list
CLIENT LIST                   # Xem tat ca connections
CLIENT INFO                   # Connection hien tai

# Latency
redis-cli -a YOUR_PASSWORD --latency
redis-cli -a YOUR_PASSWORD --latency-history
```

### Prometheus exporter

```yaml
services:
  redis-exporter:
    image: oliver006/redis_exporter:latest
    environment:
      REDIS_ADDR: redis://redis:6379
      REDIS_PASSWORD: YOUR_PASSWORD
    ports:
      - "9121:9121"
```

## Replication

```yaml
services:
  redis-master:
    image: redis:7-alpine
    command: redis-server --requirepass YOUR_PASSWORD

  redis-replica:
    image: redis:7-alpine
    command: redis-server --replicaof redis-master 6379 --masterauth YOUR_PASSWORD --requirepass YOUR_PASSWORD
```

```bash
# Kiem tra replication
INFO replication
# role: master/slave
# connected_slaves: so replica
```
