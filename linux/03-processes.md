# Linux Processes

> Ngay bat dau: ___

## Xem processes

```bash
# Process hien tai
ps aux                         # Tat ca processes
ps aux | grep nginx            # Tim process cu the
ps -ef --forest                # Xem cay process

# Top — realtime
top                            # Tong quan CPU/RAM
htop                           # Giao dien dep hon (can cai)

# Lenh nhanh
pgrep nginx                    # Tim PID theo ten
pidof nginx                    # PID cua process
```

## Kill processes

```bash
kill PID                       # SIGTERM — ngung an toan
kill -9 PID                    # SIGKILL — ep dung ngay
kill -HUP PID                  # Reload config (nginx, etc)
killall nginx                  # Kill tat ca process ten nginx
pkill -f "dotnet MyApp"        # Kill theo command line pattern
```

## Systemd

Systemd la init system quan ly services tren Linux hien dai.

```bash
# Quan ly service
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx         # Reload config khong downtime
systemctl status nginx         # Xem trang thai

# Enable/Disable
systemctl enable nginx         # Tu start khi boot
systemctl disable nginx        # Khong start khi boot

# Xem tat ca services
systemctl list-units --type=service
systemctl list-units --type=service --state=running
```

## Journalctl — doc system logs

```bash
# Xem log cua 1 service
journalctl -u nginx
journalctl -u nginx --since "1 hour ago"
journalctl -u nginx -f                      # Follow realtime

# Xem log he thong
journalctl -b                  # Log tu lan boot hien tai
journalctl --since "2026-04-25 00:00:00"
journalctl -p err              # Chi log loi

# Xem dung luong log
journalctl --disk-usage
```

## Background jobs

```bash
# Chay background
command &
nohup command &                # Khong dung khi logout

# Quan ly jobs
jobs                           # List background jobs
fg %1                         # Dua job 1 len foreground
bg %1                         # Resume job 1 o background
Ctrl+Z                        # Pause job hien tai
```

## Resource monitoring

```bash
# CPU va RAM
top -bn1 | head -5            # Snapshot nhanh
free -h                        # RAM usage
uptime                         # Load average

# Load average
# 1min 5min 15min — so sanh voi so CPU cores
# load = so CPU cores → 100% utilized
nproc                          # So CPU cores
```
