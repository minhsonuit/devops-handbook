# Connection Pooling

> Ngay bat dau: ___

## Tai sao can connection pooling

```
Khong co pooling:
  Moi request → tao connection moi → query → dong connection
  → Chi phi tao connection cao (TCP handshake, auth, SSL)
  → Hit max_connections nhanh khi traffic cao

Co pooling:
  App → Pool (giu san N connections) → reuse connection → query
  → Nhanh hon, it tai nguyen hon
  → Kiem soat duoc so connection den DB
```

## .NET — Connection pooling tu dong

```csharp
// EF Core / ADO.NET tu dong pool connections
// Quan trong: dung 1 connection string → 1 pool
"Server=db;Database=myapp;User Id=app;Password=xxx;
 Min Pool Size=5;       // Giu san 5 connections
 Max Pool Size=100;     // Toi da 100 connections
 Connection Lifetime=0; // Khong gioi han tuoi connection
 Pooling=true;"         // Mac dinh da true
```

```csharp
// Kiem tra pool trong .NET
// Moi unique connection string = 1 pool rieng
// CANH BAO: thay doi bat ky ky tu nao → pool moi
```

## PgBouncer (PostgreSQL)

PgBouncer ngoi giua app va PostgreSQL, quan ly connection pool.

```yaml
services:
  pgbouncer:
    image: edoburu/pgbouncer:latest
    environment:
      DATABASE_URL: "postgres://app_user:password@postgres:5432/myapp"
      POOL_MODE: transaction     # transaction | session | statement
      MAX_CLIENT_CONN: 1000      # Max connections tu app
      DEFAULT_POOL_SIZE: 20      # Connections den PostgreSQL
      MIN_POOL_SIZE: 5
    ports:
      - "6432:6432"              # App ket noi port nay thay vi 5432
```

### Pool modes

| Mode | Mo ta | Khi nao dung |
|------|-------|-------------|
| `session` | 1 client = 1 server connection | Simple, giong khong co PgBouncer |
| `transaction` | Share connection giua transactions | **Khuyen nghi** — hieu qua nhat |
| `statement` | Share connection giua statements | Chi cho simple queries |

```bash
# Monitor PgBouncer
psql -h localhost -p 6432 -U pgbouncer pgbouncer
SHOW POOLS;
SHOW STATS;
SHOW CLIENTS;
SHOW SERVERS;
```

## Redis connection pooling

```csharp
// StackExchange.Redis tu dong multiplexing (khong can pool truyen thong)
// 1 ConnectionMultiplexer cho toan app
var redis = ConnectionMultiplexer.Connect("redis:6379,password=xxx,abortConnect=false");
// Register as Singleton trong DI
services.AddSingleton<IConnectionMultiplexer>(redis);
```

## Monitoring connections

```sql
-- PostgreSQL: xem connections
SELECT count(*) FROM pg_stat_activity;
SELECT state, count(*) FROM pg_stat_activity GROUP BY state;
SELECT usename, count(*) FROM pg_stat_activity GROUP BY usename;

-- SQL Server
SELECT COUNT(*) FROM sys.dm_exec_sessions WHERE is_user_process = 1;
SELECT login_name, COUNT(*) FROM sys.dm_exec_sessions
WHERE is_user_process = 1 GROUP BY login_name;
```
