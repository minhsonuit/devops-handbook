# Load Balancing

> Ngay bat dau: ___

## L4 vs L7 Load Balancing

| | Layer 4 (Transport) | Layer 7 (Application) |
|---|-----|-----|
| Hoat dong o | TCP/UDP | HTTP/HTTPS |
| Nhin thay | IP, port | URL, headers, cookies |
| Toc do | Nhanh hon | Cham hon (phai parse HTTP) |
| Tinh nang | Don gian | Path routing, sticky session |
| Vi du | Azure LB, AWS NLB | Nginx, HAProxy, AWS ALB |

## Load balancing algorithms

| Algorithm | Mo ta | Khi nao dung |
|-----------|-------|--------------|
| Round Robin | Luan phien deu | Mac dinh, backend giong nhau |
| Least Connections | Gui den server it connection nhat | Request xu ly khong deu |
| IP Hash | Cung IP → cung server | Can sticky session |
| Weighted | Server manh nhan nhieu hon | Backend khong dong deu |

## Nginx load balancing

```nginx
upstream api_backend {
    # Round Robin (mac dinh)
    server 10.1.1.101:5001;
    server 10.1.1.102:5001;
    server 10.1.1.103:5001;
}

upstream api_weighted {
    server 10.1.1.101:5001 weight=3;   # Nhan 3x traffic
    server 10.1.1.102:5001 weight=1;
}

upstream api_least_conn {
    least_conn;
    server 10.1.1.101:5001;
    server 10.1.1.102:5001;
}

upstream api_ip_hash {
    ip_hash;
    server 10.1.1.101:5001;
    server 10.1.1.102:5001;
}
```

## Health checks trong Nginx

```nginx
upstream api_backend {
    server 10.1.1.101:5001 max_fails=3 fail_timeout=30s;
    server 10.1.1.102:5001 max_fails=3 fail_timeout=30s;
    server 10.1.1.103:5001 backup;     # Chi dung khi cac server khac down
}
```

- `max_fails=3`: sau 3 lan fail → danh dau down
- `fail_timeout=30s`: sau 30s thu lai
- `backup`: chi dung khi tat ca server chinh down

## Kiem tra load balancing

```bash
# Gui nhieu request, xem phan phoi
for i in $(seq 1 20); do
  curl -s http://your-lb/api/health | jq .server
done

# Xem upstream status trong log
docker logs --since 5m NGINX 2>&1 | awk '{print $NF}' | sort | uniq -c
```
