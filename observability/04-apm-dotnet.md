# APM cho .NET

> Ngay bat dau: ___

## APM la gi

Application Performance Monitoring — giam sat performance tu goc ung dung.

## .NET Diagnostics tools

```bash
# dotnet-counters — runtime metrics realtime
dotnet tool install --global dotnet-counters
dotnet-counters monitor --process-id PID

# dotnet-dump — memory dump
dotnet tool install --global dotnet-dump
dotnet-dump collect --process-id PID
dotnet-dump analyze core_20260425.dmp

# dotnet-trace — performance trace
dotnet tool install --global dotnet-trace
dotnet-trace collect --process-id PID --duration 00:00:30

# dotnet-gcdump — GC heap analysis
dotnet tool install --global dotnet-gcdump
dotnet-gcdump collect --process-id PID
```

## Metrics quan trong cho .NET app

| Metric | Y nghia | Alert khi |
|--------|---------|-----------|
| `process.runtime.dotnet.gc.collections.count` | GC frequency | Tang dot ngot |
| `process.runtime.dotnet.gc.heap.size` | Heap memory | Lien tuc tang (leak?) |
| `process.runtime.dotnet.threadpool.threads.count` | Thread pool size | Dat max |
| `process.runtime.dotnet.threadpool.queue.length` | Queued work items | > 0 lien tuc |
| `http.server.duration` | Request latency | p95 > 2s |
| `http.server.request.count` | Request rate | Drop dot ngot |

## Health check endpoints

```csharp
// NuGet: Microsoft.Extensions.Diagnostics.HealthChecks
builder.Services.AddHealthChecks()
    .AddSqlServer(connectionString, name: "sql-server")
    .AddRedis(redisConnection, name: "redis")
    .AddNpgSql(pgConnection, name: "postgresql")
    .AddUrlGroup(new Uri("http://dependency-service/health"), name: "dependency");

// Liveness — app co song khong
app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false  // Khong check dependencies
});

// Readiness — app co san sang nhan request khong
app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = _ => true   // Check tat ca dependencies
});
```

## Memory leak detection

```bash
# Thu thap 2 dumps cach nhau 5-10 phut
dotnet-gcdump collect -p PID -o dump1.gcdump
# ... doi 10 phut ...
dotnet-gcdump collect -p PID -o dump2.gcdump

# So sanh trong Visual Studio hoac dotnet-gcdump report
dotnet-gcdump report dump2.gcdump
```

Signs cua memory leak:
- `GC Heap Size` tang lien tuc
- Gen2 collections tang
- `Working Set` khong giam sau GC
