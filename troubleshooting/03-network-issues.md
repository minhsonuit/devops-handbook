# Network Issues

> Ngay bat dau: ___

## DNS khong resolve

```bash
# Kiem tra
nslookup domain.com
dig domain.com +short

# Trong Docker
docker exec CONTAINER getent hosts service-name
docker exec CONTAINER cat /etc/resolv.conf

# Fix
# - Kiem tra container co cung network khong
# - Restart Docker DNS: docker restart
# - Thu DNS public: dig @8.8.8.8 domain.com
```

## Connection refused

```bash
# Service co dang listen port do khong?
docker exec CONTAINER ss -tlnp | grep PORT

# Co the ket noi tu container khac khong?
docker exec OTHER nc -zv CONTAINER PORT

# Common causes:
# - Service chua start xong
# - Service bind 127.0.0.1 thay vi 0.0.0.0
# - Port mapping sai
```

## Timeout

```bash
# Kiem tra mang
docker exec CONTAINER ping -c 2 TARGET
traceroute TARGET

# Kiem tra firewall
iptables -L -n | grep PORT
ufw status

# Kiem tra proxy timeout
docker exec nginx nginx -T | grep timeout
```

## SSL errors

Xem chi tiet: [nginx/07-ssl-certificates.md](../nginx/07-ssl-certificates.md)
