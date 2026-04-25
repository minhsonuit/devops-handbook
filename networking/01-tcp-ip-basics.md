# TCP/IP Basics

> Ngay bat dau: ___

## OSI Model don gian

```
Layer 7 — Application   HTTP, HTTPS, DNS, SSH
Layer 4 — Transport     TCP, UDP
Layer 3 — Network       IP, ICMP, routing
Layer 2 — Data Link     MAC address, switch
Layer 1 — Physical      Cable, wifi
```

Doi voi DevOps, quan trong nhat la **Layer 4 (TCP/UDP)** va **Layer 7 (HTTP)**.

## IP Address

```bash
# Xem IP
ip addr show
ip a                           # Viet tat
hostname -I                    # Chi IP

# IPv4 private ranges
# 10.0.0.0/8        — Class A (10.x.x.x)
# 172.16.0.0/12     — Class B (172.16-31.x.x)
# 192.168.0.0/16    — Class C (192.168.x.x)
```

## Subnet & CIDR

```
/32 = 1 IP          (10.1.1.100/32)
/24 = 256 IPs       (10.1.1.0/24 → 10.1.1.0 - 10.1.1.255)
/16 = 65,536 IPs    (10.1.0.0/16 → 10.1.0.0 - 10.1.255.255)
/8  = 16 million    (10.0.0.0/8  → 10.0.0.0 - 10.255.255.255)
```

## Ports

```
Port 22    — SSH
Port 80    — HTTP
Port 443   — HTTPS
Port 5432  — PostgreSQL
Port 1433  — SQL Server
Port 6379  — Redis
Port 5001  — ASP.NET (Kestrel default)
Port 8080  — HTTP alternative
Port 9090  — Prometheus
Port 3000  — Grafana
```

```bash
# Xem port dang listen
ss -tlnp                       # TCP listening
ss -ulnp                       # UDP listening
ss -tanp                       # Tat ca TCP connections

# Kiem tra port tu xa
nc -zv 10.1.1.100 5432         # Test ket noi
telnet 10.1.1.100 5432         # Test ket noi (cu hon)
curl -v telnet://10.1.1.100:5432  # Dung curl
```

## TCP vs UDP

| | TCP | UDP |
|---|-----|-----|
| Connection | Co (3-way handshake) | Khong |
| Reliable | Co (retransmit) | Khong |
| Order | Dam bao thu tu | Khong |
| Use case | HTTP, SSH, DB | DNS, video, game |

## Routing

```bash
# Xem routing table
ip route
ip route show

# Trace route den server
traceroute google.com
mtr google.com                 # Realtime traceroute
```

## Debug tools

```bash
# Ping — kiem tra connectivity
ping -c 4 10.1.1.100

# Curl — test HTTP
curl -v https://api.example.com
curl -sI https://api.example.com    # Chi headers
curl -w "time: %{time_total}s\n" -o /dev/null -s https://api.example.com

# Netcat — test TCP port
nc -zv host port

# Tcpdump — capture packets
tcpdump -i eth0 port 80
tcpdump -i eth0 host 10.1.1.100
tcpdump -i eth0 -w capture.pcap    # Luu ra file
```
