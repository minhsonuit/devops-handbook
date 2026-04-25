# .NET DevOps Gotchas — Bay cho .NET dev lam DevOps

> Day la nhung loi ma .NET developer HAY gap khi lam DevOps — vi tu duy dev va ops khac nhau.

---

## 1. Kestrel bind 127.0.0.1 trong Docker

### Van de
App chay tot tren local nhung trong Docker tra "Connection refused".

### Nguyen nhan
Kestrel mac dinh bind `localhost` (127.0.0.1) — chi nhan request tu trong container. Docker port mapping forward den container IP, khong phai 127.0.0.1.

### Fix
```csharp
// Program.cs
builder.WebHost.UseUrls("http://0.0.0.0:5001");

// HOAC environment variable
ASPNETCORE_URLS=http://+:5001
```

```yaml
# docker-compose.yml
environment:
  - ASPNETCORE_URLS=http://+:5001
```

**Nguyen ly:** Trong Docker, `localhost` = trong container, khong phai host machine.

---

## 2. appsettings.json override order

### Van de
Config tren staging khac expected. Hoa ra environment variable override appsettings.

### Override order (.NET)
```
1. appsettings.json                    ← thap nhat
2. appsettings.{Environment}.json
3. User secrets (dev only)
4. Environment variables               ← cao nhat
5. Command line args                   ← cao nhat nhat
```

### Bay
```json
// appsettings.json
{
  "ConnectionStrings": {
    "Default": "Server=prod-db;..."
  }
}
```

```yaml
# docker-compose.yml
environment:
  - ConnectionStrings__Default=Server=staging-db;...
  # Dung __ (double underscore) cho nested config
```

**Environment variable THANG appsettings.json.** Nhieu dev khong biet dieu nay.

---

## 3. EF Core migration trong Docker

### Van de
`dotnet ef database update` chay tren dev nhung khong chay trong Docker.

### Nguyen nhan
- Docker image dung runtime (khong co SDK)
- EF tools can SDK

### Fix
```csharp
// Apply migration trong code (production-safe)
using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    db.Database.Migrate();
}
```

**CANH BAO:** Auto-migrate trong production co risk. Nen:
1. Test migration tren staging truoc
2. Co rollback plan
3. Backup truoc khi migrate

---

## 4. HttpClient lifecycle — Socket exhaustion

### Van de
App chay 1 ngay thi bat dau loi "SocketException: Address already in use".

### Nguyen nhan
```csharp
// SAI — tao HttpClient moi moi lan → socket leak
public async Task<string> GetData()
{
    using var client = new HttpClient();  // ❌ Socket leak!
    return await client.GetStringAsync("http://api/data");
}
```

### Fix
```csharp
// DUNG — dung IHttpClientFactory
builder.Services.AddHttpClient("MyApi", client =>
{
    client.BaseAddress = new Uri("http://api/");
    client.Timeout = TimeSpan.FromSeconds(30);
});

// Su dung
public class MyService
{
    private readonly HttpClient _client;
    public MyService(IHttpClientFactory factory)
    {
        _client = factory.CreateClient("MyApi");
    }
}
```

**Nguyen ly:** TCP socket sau khi close van o trang thai TIME_WAIT 2-4 phut. Tao nhieu HttpClient = het socket.

---

## 5. Timezone trong Docker

### Van de
DateTime trong log/database sai gio.

### Nguyen nhan
Docker container mac dinh dung UTC. App .NET dung `DateTime.Now` = UTC, khong phai local time.

### Fix
```csharp
// DUNG — luon dung UTC
var now = DateTime.UtcNow;           // ✅
var now = DateTimeOffset.UtcNow;     // ✅ (tot hon)

// SAI — phu thuoc server timezone
var now = DateTime.Now;              // ❌ Ket qua khac nhau tren moi server
```

```yaml
# Neu bat buoc phai set timezone
environment:
  - TZ=Asia/Ho_Chi_Minh
```

**Nguyen ly:** Trong distributed system, **luon luu UTC**. Chi convert sang local khi hien thi cho user.

---

## 6. Memory limits va GC

### Van de
Container bi OOMKilled du app chi dung 200MB.

### Nguyen nhan
.NET GC khong biet container memory limit (truoc .NET 6). GC nghi co nhieu RAM → allocate nhieu → OOMKill.

### Fix
```yaml
environment:
  - DOTNET_GCHeapHardLimit=0x1E000000    # 480MB (hex)
  # HOAC
  - DOTNET_GCHeapHardLimitPercent=0x50   # 50% cua container limit
deploy:
  resources:
    limits:
      memory: 512M
```

**.NET 6+** tu dong detect container limit. Nhung nen dat explicit limit.

---

## 7. Health check endpoint khong check dependencies

### Van de
Health check tra "Healthy" nhung app thuc te khong hoat dong vi DB die.

### Nguyen nhan
```csharp
// SAI — chi check app song, khong check dependencies
app.MapHealthChecks("/health");  // Luon tra 200
```

### Fix
```csharp
// Tach liveness va readiness
app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false   // Chi check app process
});

app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = _ => true    // Check DB, Redis, etc.
});
```

**Nguyen ly:**
- **Liveness:** "App co song khong?" → restart neu chet
- **Readiness:** "App co san sang nhan request khong?" → bo khoi LB neu chua san sang

---

## 8. Sync over Async — Thread starvation

### Van de
API handle duoc 50 request/s du server con nhieu CPU.

### Nguyen nhan
```csharp
// SAI — block async call → thread pool starvation
public string GetData()
{
    var result = _httpClient.GetStringAsync("http://api/data").Result;  // ❌ .Result blocks!
    return result;
}
```

### Fix
```csharp
// DUNG — async all the way
public async Task<string> GetData()
{
    return await _httpClient.GetStringAsync("http://api/data");  // ✅
}
```

**Nguyen ly:** .NET thread pool co gioi han. Block 1 thread = 1 thread khong xu ly request khac. 50 threads blocked = app hung.
