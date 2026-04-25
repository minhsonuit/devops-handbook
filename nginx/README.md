# Nginx Docs

Bo tai lieu nay tach rieng Nginx theo tung nhom de de tra cuu:

## Tai lieu

- [01-overview.md](01-overview.md) — Tong quan Nginx, vai tro, luong request
- [02-config-guide.md](02-config-guide.md) — Huong dan viet config: upstream, location, best practices
- [03-tuning-and-params.md](03-tuning-and-params.md) — Thong so tuning: timeout, connection, keepalive
- [04-monitoring-and-logs.md](04-monitoring-and-logs.md) — Monitoring, doc log, soi request cham
- [05-operations-checklist.md](05-operations-checklist.md) — Checklist truoc/sau khi sua config, khi gap loi
- [06-security.md](06-security.md) — Bao mat: server_tokens, headers, rate limiting, whitelist IP
- [07-ssl-certificates.md](07-ssl-certificates.md) — SSL/TLS hardening, Let's Encrypt, quan ly cert
- [08-troubleshooting.md](08-troubleshooting.md) — Xu ly su co: decision tree 502/504/403/404/413/499
- [09-gzip-and-performance.md](09-gzip-and-performance.md) — Gzip, caching, buffer, network tuning

## Config mau

- [nginx.sample.conf](nginx.sample.conf) — Config mau production-ready, da ap dung tat ca best practices

## Nen doc file nao truoc

| Muc dich | File |
|----------|------|
| Hieu tong quan va luong request | `01-overview.md` |
| Viet hoac review config | `02-config-guide.md` |
| Chinh timeout, connection, keepalive | `03-tuning-and-params.md` |
| Monitor, check log, soi request cham | `04-monitoring-and-logs.md` |
| Checklist thao tac production | `05-operations-checklist.md` |
| Bao mat, rate limit, chan bot | `06-security.md` |
| SSL/TLS, cert renewal | `07-ssl-certificates.md` |
| Gap loi va can debug | `08-troubleshooting.md` |
| Optimize performance | `09-gzip-and-performance.md` |
| Can config san de dung | `nginx.sample.conf` |
