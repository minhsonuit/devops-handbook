# Benchmarking

> Ngay bat dau: ___

## wrk — HTTP benchmark

```bash
# Cai dat: brew install wrk

# Basic: 10 threads, 100 connections, 30 giay
wrk -t10 -c100 -d30s http://localhost:5001/api/health

# Voi script Lua
wrk -t10 -c100 -d30s -s post.lua http://localhost:5001/api/orders
```

## ab (Apache Bench)

```bash
# 1000 requests, 50 concurrent
ab -n 1000 -c 50 http://localhost:5001/api/health

# Voi headers
ab -n 1000 -c 50 -H "Authorization: Bearer TOKEN" http://localhost:5001/api/orders
```

## curl timing

```bash
# Do thoi gian chi tiet cua 1 request
curl -w "\
  DNS:        %{time_namelookup}s\n\
  Connect:    %{time_connect}s\n\
  TLS:        %{time_appconnect}s\n\
  TTFB:       %{time_starttransfer}s\n\
  Total:      %{time_total}s\n\
  Size:       %{size_download} bytes\n\
  Status:     %{http_code}\n" \
  -o /dev/null -s https://api-pos.pharmacity.vn/health
```

## Database benchmark

```bash
# pgbench — PostgreSQL
docker exec postgres pgbench -U app_user -d myapp -i    # Init
docker exec postgres pgbench -U app_user -d myapp -c 10 -j 4 -T 60
# -c 10: 10 clients, -j 4: 4 threads, -T 60: 60 seconds

# redis-benchmark
docker exec redis redis-benchmark -a YOUR_PASSWORD -q -n 10000
docker exec redis redis-benchmark -a YOUR_PASSWORD -q -t set,get -n 100000
```

## .NET BenchmarkDotNet

```csharp
// NuGet: BenchmarkDotNet
[MemoryDiagnoser]
public class MyBenchmarks
{
    [Benchmark]
    public async Task GetOrderById()
    {
        await _service.GetByIdAsync(1);
    }

    [Benchmark]
    public async Task SearchOrders()
    {
        await _service.SearchAsync("keyword");
    }
}

// Chay: dotnet run -c Release
```
