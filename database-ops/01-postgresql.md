# PostgreSQL Operations

> Ngay bat dau: ___

## Chay PostgreSQL trong Docker

```yaml
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: YOUR_PASSWORD
      POSTGRES_DB: myapp
    ports:
      - "5432:5432"
    volumes:
      - pg_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app_user -d myapp"]
      interval: 10s
      timeout: 5s
      retries: 5
    deploy:
      resources:
        limits:
          memory: 1G
    restart: unless-stopped

volumes:
  pg_data:
```

## Ket noi va lenh co ban

```bash
# Ket noi tu host
psql -h localhost -U app_user -d myapp

# Ket noi tu Docker
docker exec -it postgres psql -U app_user -d myapp

# Lenh trong psql
\l                     # List databases
\dt                    # List tables
\d+ table_name         # Describe table
\du                    # List users/roles
\conninfo              # Connection info
\q                     # Quit
```

## Backup va Restore

### pg_dump (logical backup)

```bash
# Backup 1 database
docker exec postgres pg_dump -U app_user -d myapp > backup_$(date +%Y%m%d).sql

# Backup compress
docker exec postgres pg_dump -U app_user -d myapp -Fc > backup.dump

# Backup chi schema
docker exec postgres pg_dump -U app_user -d myapp --schema-only > schema.sql

# Backup chi data
docker exec postgres pg_dump -U app_user -d myapp --data-only > data.sql

# Backup tat ca databases
docker exec postgres pg_dumpall -U postgres > all_databases.sql
```

### Restore

```bash
# Tu SQL file
cat backup.sql | docker exec -i postgres psql -U app_user -d myapp

# Tu dump file (compress)
docker exec -i postgres pg_restore -U app_user -d myapp < backup.dump

# Tao DB moi va restore
docker exec postgres createdb -U postgres myapp_restored
cat backup.sql | docker exec -i postgres psql -U postgres -d myapp_restored
```

### Automated backup script

```bash
#!/bin/bash
set -euo pipefail

BACKUP_DIR="/backup/postgres/$(date +%Y-%m-%d)"
CONTAINER="postgres"
DB_USER="app_user"
DB_NAME="myapp"
KEEP_DAYS=7

mkdir -p "$BACKUP_DIR"

# Backup
docker exec "$CONTAINER" pg_dump -U "$DB_USER" -d "$DB_NAME" -Fc \
  > "$BACKUP_DIR/${DB_NAME}_$(date +%H%M).dump"

# Verify
if [ $? -eq 0 ]; then
    echo "$(date): Backup OK — $(ls -lh $BACKUP_DIR/*.dump | tail -1)"
else
    echo "$(date): Backup FAILED" >&2
    exit 1
fi

# Cleanup
find /backup/postgres -type d -mtime +$KEEP_DAYS -exec rm -rf {} + 2>/dev/null
```

## Monitoring PostgreSQL

### Queries huu ich

```sql
-- Active connections
SELECT count(*) FROM pg_stat_activity;

-- Connections theo state
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;

-- Long running queries (> 30s)
SELECT pid, now() - pg_stat_activity.query_start AS duration, query, state
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '30 seconds'
AND state != 'idle'
ORDER BY duration DESC;

-- Table sizes
SELECT schemaname, tablename,
       pg_size_pretty(pg_total_relation_size(schemaname || '.' || tablename)) AS total_size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname || '.' || tablename) DESC
LIMIT 20;

-- Index usage
SELECT schemaname, tablename, indexname,
       idx_scan AS times_used,
       pg_size_pretty(pg_relation_size(indexrelid)) AS index_size
FROM pg_stat_user_indexes
ORDER BY idx_scan ASC
LIMIT 20;

-- Unused indexes (can xem xet xoa)
SELECT schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
AND schemaname = 'public';

-- Cache hit ratio (nen > 99%)
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit) AS heap_hit,
  round(sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read))::numeric * 100, 2) AS ratio
FROM pg_statio_user_tables;

-- Deadlocks
SELECT deadlocks FROM pg_stat_database WHERE datname = 'myapp';

-- Slow queries (nen bat pg_stat_statements)
-- CREATE EXTENSION pg_stat_statements;
SELECT query, calls, mean_exec_time, total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;
```

### Prometheus exporter

```yaml
services:
  postgres-exporter:
    image: prometheuscommunity/postgres-exporter:latest
    environment:
      DATA_SOURCE_NAME: "postgresql://app_user:YOUR_PASSWORD@postgres:5432/myapp?sslmode=disable"
    ports:
      - "9187:9187"
```

## Tuning co ban

### postgresql.conf

```ini
# Memory — dat theo RAM available
shared_buffers = 256MB           # 25% RAM
effective_cache_size = 768MB     # 75% RAM
work_mem = 16MB                  # Per operation
maintenance_work_mem = 128MB     # VACUUM, CREATE INDEX

# Connections
max_connections = 200

# WAL
wal_buffers = 16MB
checkpoint_completion_target = 0.9

# Logging
log_min_duration_statement = 500   # Log query > 500ms
log_statement = 'none'             # Production: 'none', debug: 'all'

# Stats
shared_preload_libraries = 'pg_stat_statements'
```

```bash
# Xem setting hien tai
docker exec postgres psql -U postgres -c "SHOW shared_buffers;"
docker exec postgres psql -U postgres -c "SHOW max_connections;"

# Xem tat ca settings khac default
docker exec postgres psql -U postgres -c \
  "SELECT name, setting, unit FROM pg_settings WHERE source != 'default' ORDER BY name;"
```

## Replication co ban

### Streaming replication

```yaml
# Primary
services:
  postgres-primary:
    image: postgres:16
    environment:
      POSTGRES_REPLICATION_MODE: master
      POSTGRES_REPLICATION_USER: repl_user
      POSTGRES_REPLICATION_PASSWORD: repl_password

# Replica (read-only)
  postgres-replica:
    image: postgres:16
    environment:
      POSTGRES_REPLICATION_MODE: slave
      POSTGRES_MASTER_HOST: postgres-primary
      POSTGRES_MASTER_PORT_NUMBER: 5432
```

```sql
-- Kiem tra replication status (tren primary)
SELECT client_addr, state, sent_lsn, write_lsn, replay_lsn
FROM pg_stat_replication;

-- Kiem tra replica lag
SELECT now() - pg_last_xact_replay_timestamp() AS replication_lag;
```

## VACUUM

```sql
-- VACUUM thu cong
VACUUM VERBOSE table_name;
VACUUM FULL table_name;       -- Giam kich thuoc file, nhung lock table
ANALYZE table_name;            -- Cap nhat statistics

-- Xem autovacuum status
SELECT schemaname, relname, last_vacuum, last_autovacuum, n_dead_tup
FROM pg_stat_user_tables
ORDER BY n_dead_tup DESC;
```
