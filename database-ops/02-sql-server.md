# SQL Server Operations

> Ngay bat dau: ___

## Chay SQL Server trong Docker

```yaml
services:
  mssql:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      ACCEPT_EULA: "Y"
      MSSQL_SA_PASSWORD: "YOUR_Strong_Password1!"
      MSSQL_PID: "Developer"    # Developer, Express, Standard, Enterprise
    ports:
      - "1433:1433"
    volumes:
      - mssql_data:/var/opt/mssql
    healthcheck:
      test: /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "$$MSSQL_SA_PASSWORD" -Q "SELECT 1" -C || exit 1
      interval: 15s
      timeout: 10s
      retries: 5
    deploy:
      resources:
        limits:
          memory: 2G
    restart: unless-stopped

volumes:
  mssql_data:
```

## Ket noi

```bash
# sqlcmd tu host
sqlcmd -S localhost,1433 -U sa -P 'YOUR_Strong_Password1!'

# sqlcmd tu Docker
docker exec -it mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'YOUR_Strong_Password1!' -C
```

## Backup va Restore

```bash
# Backup
docker exec mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa \
  -P 'YOUR_PASSWORD' -C \
  -Q "BACKUP DATABASE [MyDB] TO DISK = '/var/opt/mssql/backup/MyDB_$(date +%Y%m%d).bak'"

# Restore
docker exec mssql /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa \
  -P 'YOUR_PASSWORD' -C \
  -Q "RESTORE DATABASE [MyDB] FROM DISK = '/var/opt/mssql/backup/MyDB.bak' WITH REPLACE"

# Copy backup ra host
docker cp mssql:/var/opt/mssql/backup/MyDB.bak ./MyDB.bak
```

## Monitoring queries

```sql
-- Active sessions
SELECT session_id, login_name, status, command, wait_type, cpu_time, reads, writes
FROM sys.dm_exec_sessions WHERE is_user_process = 1;

-- Long running queries
SELECT r.session_id, r.start_time, r.status, r.command,
       DATEDIFF(SECOND, r.start_time, GETDATE()) AS duration_seconds,
       t.text AS query_text
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE r.session_id > 50
ORDER BY r.start_time;

-- Table sizes
SELECT t.NAME AS TableName,
       p.rows AS RowCounts,
       CAST(ROUND(SUM(a.total_pages) * 8.0 / 1024, 2) AS DECIMAL(18,2)) AS TotalSpaceMB
FROM sys.tables t
INNER JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN sys.allocation_units a ON p.partition_id = a.container_id
GROUP BY t.Name, p.Rows
ORDER BY TotalSpaceMB DESC;

-- Missing indexes
SELECT TOP 20
  ROUND(s.avg_total_user_cost * s.avg_user_impact * (s.user_seeks + s.user_scans), 0) AS [Impact],
  d.statement, d.equality_columns, d.inequality_columns, d.included_columns
FROM sys.dm_db_missing_index_details d
JOIN sys.dm_db_missing_index_groups g ON d.index_handle = g.index_handle
JOIN sys.dm_db_missing_index_group_stats s ON g.index_group_handle = s.group_handle
ORDER BY [Impact] DESC;

-- Wait stats (performance bottleneck)
SELECT TOP 10 wait_type, wait_time_ms / 1000.0 AS wait_time_sec,
       signal_wait_time_ms / 1000.0 AS signal_wait_sec
FROM sys.dm_os_wait_stats
WHERE wait_type NOT IN ('SLEEP_TASK','BROKER_TO_FLUSH','SQLTRACE_BUFFER_FLUSH',
                         'CLR_AUTO_EVENT','CLR_MANUAL_EVENT','LAZYWRITER_SLEEP')
ORDER BY wait_time_ms DESC;

-- Deadlocks
SELECT * FROM sys.dm_exec_requests WHERE blocking_session_id > 0;
```

## Tuning

```sql
-- Xem memory usage
SELECT physical_memory_in_use_kb / 1024 AS memory_used_mb,
       locked_page_allocations_kb / 1024 AS locked_mb,
       total_virtual_address_space_kb / 1024 AS virtual_mb
FROM sys.dm_os_process_memory;

-- Cau hinh max memory (nen set, khong de unlimited)
EXEC sp_configure 'max server memory (MB)', 1536;
RECONFIGURE;

-- Bat Query Store (tu SQL 2016)
ALTER DATABASE [MyDB] SET QUERY_STORE = ON;
```
