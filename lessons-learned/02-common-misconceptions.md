# Common Misconceptions — Nhung dieu tuong dung nhung sai

> "Half of what you know is wrong. The problem is, you don't know which half."

---

## Docker & Containers

### ❌ "Docker = Virtual Machine"

**Thuc te:** Container KHONG phai VM. Container chia se kernel voi host. VM co kernel rieng.

```
VM:        App → Guest OS → Hypervisor → Host OS → Hardware
Container: App → Container Runtime → Host OS → Hardware
```

**Hau qua tin sai:** 
- Nghi container isolated 100% nhu VM → false sense of security
- Container co the escape neu co loi kernel
- Khong dat security limits vi "no giong VM ma"

### ❌ "Container la stateless, mat data khi restart"

**Thuc te:** Container filesystem la ephemeral. Nhung **volumes thi persist** qua restarts.

```yaml
volumes:
  - db_data:/var/lib/postgresql/data    # Data nay khong mat
```

**Lam tuong nguy hiem:** "Restart container = mat database" → khong dung neu dung volumes.
**Lam tuong nguy hiem hon:** "Data trong container luon an toan" → SAI, data khong trong volume se mat.

### ❌ "Docker image latest luon la moi nhat"

**Thuc te:** `latest` chi la 1 tag — khong co gi dac biet. Neu ban pull `nginx:latest` hom nay, va repo update ngay mai, ban van dang dung image cu cho den khi pull lai.

**Best practice:** Pin version cu the: `nginx:1.25.4`, `postgres:16.2`

### ❌ "Container nho = container an toan"

**Thuc te:** Image nho giam attack surface, nhung khong dam bao an toan.

```dockerfile
# Nho nhung van chay root → khong an toan
FROM alpine
COPY app /app
CMD ["/app"]

# Lon hon nhung an toan hon
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine
USER 1000
COPY --from=build /app .
```

---

## Kubernetes

### ❌ "K8s tu dong fix moi thu"

**Thuc te:** K8s restart pod khi crash, nhung:
- Khong fix memory leak (chi restart vong lap)
- Khong fix slow queries
- Khong fix sai logic

K8s la **self-healing cho infrastructure**, khong phai cho application bugs.

### ❌ "Replicas = 3 la du HA"

**Thuc te:** 3 replicas khong co nghia la HA neu:
- Tat ca tren 1 node → node die = mat het
- Khong co PDB → rolling update xoa het cung luc
- Khong co health check → traffic gui den pod dang crash

```yaml
# Thieu nay thi 3 replicas vo nghia:
affinity:
  podAntiAffinity: ...     # Trai pods len nhieu nodes
livenessProbe: ...          # Restart khi unhealthy
readinessProbe: ...         # Bo khoi LB khi khong san sang
```

### ❌ "Helm chart co san thi cu install la xong"

**Thuc te:** Helm charts mac dinh thuong khong an toan, khong optimized:
- Khong co resource limits
- Password mac dinh
- Persistence co the tat
- Network policy khong co

**Luon doc va customize `values.yaml`** truoc khi install.

---

## Monitoring

### ❌ "Nhieu metrics = monitor tot"

**Thuc te:** 1000 metrics ma khong ai nhin = monitor te.

Quan trong: **bao nhieu metric duoc gan alert va co ai phan hoi?**

```
Tot:  5 metrics + 5 alerts co action plan
Te:   500 metrics + 0 alerts + dashboard dep
```

### ❌ "CPU cao = server qua tai"

**Thuc te:** CPU 80% co the hoan toan binh thuong neu:
- Response time van tot
- Error rate = 0
- Ban dang dung CPU efficiently

**CPU thap moi la dau hieu xau** neu di kem voi response time cao → bottleneck o I/O (DB, disk, network), khong phai CPU.

### ❌ "Log moi thu se giup debug"

**Thuc te:** Log qua nhieu = noise. Khi co incident, ban se khong tim duoc gi trong 10GB log/ngay.

```
Level Production:
- Information: Business events (order created, user login)
- Warning: Recoverable issues (cache miss, retry)
- Error: Failed operations
- Verbose/Debug: TAT trong production
```

---

## Networking

### ❌ "HTTPS = an toan"

**Thuc te:** HTTPS chi bao ve **transport** (data in transit). Khong bao ve:
- SQL injection
- XSS
- Sai logic business
- Leaked credentials
- Server misconfiguration

### ❌ "Firewall block tat ca = an toan"

**Thuc te:** Firewall chi la 1 lop. Tan cong thuong di qua port 443 (HTTPS) — port ma ban phai mo.

---

## CI/CD

### ❌ "CI/CD nhanh = tot"

**Thuc te:** Pipeline 30 giay ma khong co test = deploy bug nhanh hon.

```
Pipeline 30s, deploy 10x/ngay, khong test → disaster
Pipeline 10 phut, deploy 3x/ngay, co test → reliable
```

### ❌ "100% code coverage = khong co bug"

**Thuc te:** Coverage do % code duoc chay, khong do % logic duoc kiem tra.

```csharp
// 100% coverage nhung van co bug
[Test]
public void Add_Returns_Sum()
{
    Assert.AreEqual(4, Calculator.Add(2, 2));  // Quen test: Add(int.MaxValue, 1)
}
```
