# DNS

> Ngay bat dau: ___

## DNS la gi

DNS chuyen domain name → IP address.

```
Browser → DNS Resolver → Root NS → TLD NS → Authoritative NS → IP
```

## Record types

| Type | Muc dich | Vi du |
|------|----------|-------|
| A | Domain → IPv4 | api.example.com → 10.1.1.100 |
| AAAA | Domain → IPv6 | api.example.com → ::1 |
| CNAME | Alias | www.example.com → example.com |
| MX | Mail server | example.com → mail.example.com |
| TXT | Text (SPF, DKIM) | v=spf1 include:... |
| NS | Name server | example.com → ns1.provider.com |
| SRV | Service location | _http._tcp.example.com |
| PTR | Reverse DNS (IP → domain) | |

## Lenh tra cuu DNS

```bash
# nslookup
nslookup api.example.com
nslookup -type=MX example.com

# dig (chi tiet hon)
dig api.example.com
dig api.example.com +short          # Chi IP
dig api.example.com MX              # MX records
dig @8.8.8.8 api.example.com       # Dung DNS server cu the

# host
host api.example.com
host -t MX example.com
```

## DNS trong Docker

```bash
# Docker tu dong DNS cho containers trong cung network
# Container goi nhau bang ten service
docker exec api sh -c "getent hosts db"

# Xem DNS config
docker exec api cat /etc/resolv.conf

# Docker DNS server mac dinh: 127.0.0.11
```

## /etc/hosts

```bash
# Local DNS override
cat /etc/hosts
# 127.0.0.1  localhost
# 10.1.1.100 api-pos.pharmacity.vn

# Huu ich cho test truoc khi doi DNS that
```

## Troubleshooting DNS

```bash
# DNS khong resolve?
# 1. Kiem tra /etc/resolv.conf
cat /etc/resolv.conf

# 2. Thu voi DNS public
dig @8.8.8.8 domain.com

# 3. Xoa DNS cache
# Ubuntu: systemd-resolve --flush-caches
# macOS: sudo dscacheutil -flushcache
```
