# Troubleshooting Methodology

> Ngay bat dau: ___

## Framework: Observe → Hypothesize → Test → Fix

```
1. OBSERVE  — Thu thap thong tin: logs, metrics, errors
2. HYPOTHESIZE — Dua ra gia thuyet nguyen nhan
3. TEST    — Kiem chung gia thuyet
4. FIX     — Ap dung sua chua
5. VERIFY  — Xac nhan da fix xong
6. DOCUMENT — Ghi lai de khong lap lai
```

## Buoc 1: Thu thap thong tin

```bash
# He thong
uptime                         # Load average
free -h                        # RAM
df -h                          # Disk
top -bn1 | head -20            # CPU/process

# Container
docker ps                     # Container status
docker stats --no-stream      # Resource usage
docker logs --since 30m CONTAINER 2>&1 | tail -50

# Network
ss -tlnp                      # Ports listening
curl -v http://localhost:5001/health
```

## Buoc 2: Thu hep pham vi

| Trieu chung | Thu hep |
|-------------|---------|
| Tat ca users bi loi | Infra / backend |
| Chi 1 user bi loi | Session / data |
| Cham dan | Memory leak / disk full |
| Dot ngot | Deploy / config change / dependency down |

## Buoc 3: Kiem chung

- Thay doi 1 thu duy nhat moi lan
- Co the revert ngay
- Ghi lai ket qua

## Template postmortem

```
# Incident: [Ten su co]
- Thoi gian: [bat dau] → [ket thuc]
- Impact: [ai bi anh huong, muc do]
- Root cause: [nguyen nhan goc]
- Timeline: [dien bien chi tiet]
- Fix: [da sua gi]
- Prevention: [lam gi de khong lap lai]
```
