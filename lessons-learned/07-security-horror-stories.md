# Security Horror Stories — Loi bao mat kinh dien

> "Security la chuoi — chi can 1 mat xich yeu la du."

---

## 1. Exposed .env file

### Chuyen gi xay ra
File `.env` chứa DB password, API keys, bi accessible qua web server:
```
https://example.com/.env
```

### Nguyen nhan
- Nginx/Apache khong block dotfiles
- `.env` nam trong web root

### Fix
```nginx
# Nginx — block tat ca dotfiles
location ~ /\. {
    deny all;
    return 404;
}
```

### Bai hoc
- Khong bao gio dat secrets trong web root
- Block dotfiles trong web server config
- Scan public URL bang `curl https://domain/.env`

---

## 2. Debug mode tren production

### Chuyen gi xay ra
`ASPNETCORE_ENVIRONMENT=Development` tren production → Developer Exception Page hien thi:
- Stack trace day du
- Source code snippets
- Environment variables (co the co secrets)

### Fix
```yaml
environment:
  ASPNETCORE_ENVIRONMENT: Production    # KHONG BAO GIO dung Development tren prod
```

```csharp
// Kiem tra trong code
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();    // Chi dev
}
else
{
    app.UseExceptionHandler("/error");  // Production — khong leak thong tin
}
```

---

## 3. SQL Injection — van ton tai 2026

### Chuyen gi xay ra
```csharp
// ❌ NGUY HIEM
var sql = $"SELECT * FROM Users WHERE Name = '{input}'";

// Input: ' OR '1'='1'; DROP TABLE Users; --
// → SELECT * FROM Users WHERE Name = '' OR '1'='1'; DROP TABLE Users; --'
```

### Fix
```csharp
// ✅ Parameterized query
var sql = "SELECT * FROM Users WHERE Name = @name";
cmd.Parameters.AddWithValue("@name", input);

// ✅ EF Core — an toan mac dinh
var user = await _context.Users.FirstOrDefaultAsync(u => u.Name == input);
```

### Bai hoc
- KHONG BAO GIO noi string voi SQL
- EF Core/Dapper voi parameters = an toan
- Stored procedures cung co the bi inject neu dung `EXEC(@sql)`

---

## 4. Default credentials

### Chuyen gi xay ra
Cai Redis, RabbitMQ, Grafana ma khong doi password mac dinh. Bot scan internet tim duoc trong 24h.

### Danh sach default can doi NGAY

| Service | Default | Port |
|---------|---------|------|
| Redis | no password | 6379 |
| RabbitMQ | guest/guest | 5672, 15672 |
| Grafana | admin/admin | 3000 |
| Elasticsearch | no auth | 9200 |
| MongoDB | no auth | 27017 |
| PostgreSQL | postgres/postgres | 5432 |
| Kibana | no auth | 5601 |

### Fix
- Doi password NGAY khi cai
- Khong expose port ra internet neu khong can
- Dung firewall restrict IP

---

## 5. Exposed management ports

### Chuyen gi xay ra
Docker daemon (port 2375) expose ra internet → attacker chay container voi mount root filesystem → full server access.

### Cac port KHONG BAO GIO expose ra public

| Port | Service | Risk |
|------|---------|------|
| 2375 | Docker API (unencrypted) | Full server control |
| 2376 | Docker API (TLS) | Full server control |
| 6379 | Redis | Data access, RCE |
| 9200 | Elasticsearch | Data access |
| 5601 | Kibana | Data access |
| 15672 | RabbitMQ Management | Queue manipulation |
| 8080 | K8s API (nếu ko auth) | Cluster control |

### Fix
```yaml
# Chi bind localhost
ports:
  - "127.0.0.1:6379:6379"    # ✅ Chi truy cap tu local
  # KHONG:
  - "6379:6379"              # ❌ Expose ra tat ca interfaces
```

---

## 6. JWT secret qua don gian

### Chuyen gi xay ra
```json
{
  "Jwt": {
    "Secret": "mysecretkey"    // Brute-force trong 1 giay
  }
}
```

### Fix
```bash
# Tao secret manh
openssl rand -base64 64
```

```json
{
  "Jwt": {
    "Secret": "BASE64_ENCODED_JWT_SECRET_HERE="
  }
}
```

---

## 7. Docker socket mount — "God mode"

### Chuyen gi xay ra
```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

Container co docker socket = **co quyen ngang root tren host**.

### Khi nao chap nhan duoc
- CI/CD runner (can build Docker images)
- Monitoring agents (Portainer, Prometheus)
- **KHONG BAO GIO** cho application container

### Giam thieu
- Read-only mount khi co the: `:ro`
- Dung Docker socket proxy (TCP proxy voi ACL)

---

## Security checklist cho moi project

```
Authentication & Authorization:
- [ ] Doi tat ca default passwords
- [ ] JWT secret >= 64 bytes
- [ ] RBAC cho moi user/service account
- [ ] Disable root login (SSH)

Network:
- [ ] HTTPS everywhere
- [ ] Management ports chi bind localhost
- [ ] Firewall default deny
- [ ] Block dotfiles trong web server

Application:
- [ ] Environment = Production
- [ ] Parameterized queries
- [ ] Security headers (HSTS, CSP, X-Frame-Options)
- [ ] Rate limiting
- [ ] Input validation

Infrastructure:
- [ ] Containers chay non-root
- [ ] Read-only filesystem
- [ ] Resource limits
- [ ] Image scanning trong CI
- [ ] Secrets trong vault, KHONG trong code

Monitoring:
- [ ] Alert on suspicious activity
- [ ] Audit logging
- [ ] Failed login monitoring
```
