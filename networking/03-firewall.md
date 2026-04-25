# Firewall

> Ngay bat dau: ___

## UFW (Ubuntu Firewall — de dung)

```bash
# Bat/tat
sudo ufw enable
sudo ufw disable
sudo ufw status verbose

# Cho phep port
sudo ufw allow 22/tcp          # SSH
sudo ufw allow 80/tcp          # HTTP
sudo ufw allow 443/tcp         # HTTPS
sudo ufw allow 5432/tcp        # PostgreSQL

# Cho phep tu IP cu the
sudo ufw allow from 10.1.0.0/16 to any port 5432

# Chan
sudo ufw deny 3306/tcp
sudo ufw deny from 192.168.1.100

# Xoa rule
sudo ufw delete allow 80/tcp
sudo ufw status numbered       # Xem so thu tu
sudo ufw delete 3              # Xoa rule so 3

# Reset
sudo ufw reset
```

## iptables (Linux truyen thong)

```bash
# Xem rules
sudo iptables -L -n -v
sudo iptables -L -n --line-numbers

# Cho phep port
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Cho phep tu IP range
sudo iptables -A INPUT -s 10.1.0.0/16 -p tcp --dport 5432 -j ACCEPT

# Chan tat ca con lai
sudo iptables -A INPUT -p tcp --dport 5432 -j DROP

# Xoa rule
sudo iptables -D INPUT -p tcp --dport 80 -j ACCEPT

# Luu rules (Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4
```

## Security groups (Cloud)

Tuong tu firewall nhung o muc cloud (Azure NSG, AWS SG):

```
Inbound rules:
- Allow SSH (22) from my IP
- Allow HTTP (80) from anywhere
- Allow HTTPS (443) from anywhere
- Allow app port (5001) from VNet only
- Deny all others (implicit)

Outbound rules:
- Allow all (default)
```

## Troubleshooting firewall

```bash
# Kiem tra port co mo khong (tu may khac)
nc -zv server-ip 5432
nmap -p 5432 server-ip

# Kiem tra firewall rules
sudo ufw status
sudo iptables -L -n

# Tam tat firewall de test
sudo ufw disable                # Nho bat lai!
```
