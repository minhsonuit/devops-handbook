# OS Hardening

> Ngay bat dau: ___

## Checklist co ban

- [ ] Cap nhat OS thuong xuyen: `apt update && apt upgrade`
- [ ] Tat SSH root login: `PermitRootLogin no` trong `/etc/ssh/sshd_config`
- [ ] Dung SSH key thay vi password
- [ ] Bat firewall, default deny
- [ ] Disable services khong can
- [ ] Set timezone va NTP

## SSH hardening

```bash
# /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
```

```bash
sudo systemctl restart sshd
```

## Auto security updates

```bash
# Ubuntu
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

## Fail2ban

```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
# Cau hinh: /etc/fail2ban/jail.local
```
