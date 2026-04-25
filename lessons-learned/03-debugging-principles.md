# Debugging Principles — Tu duy First-Principles

> "Khong phai debug sai, ma la quan sat sai."

---

## Nguyen ly 1: Thay doi 1 thu duy nhat moi lan

```
❌ Thay doi 3 config cung luc → van loi → khong biet config nao lam loi
✅ Thay doi 1 config → test → khong fix → revert → thu config tiep theo
```

Day la nguyen ly khoa hoc co ban (controlled experiment) ma dev thuong bo qua khi panic.

---

## Nguyen ly 2: "Recently changed" la nghi pham so 1

```
He thong chay tot 6 thang.
Hom nay loi.

Hoi: Co gi thay doi khong?
- Deploy moi?
- Config change?
- Certificate het han?
- DNS doi?
- Dependency update?
- Traffic tang dot bien?
- OS auto-update?
```

**90% su co production xay ra SAU khi co thay doi.**

```bash
# Kiem tra thay doi gan day
git log --oneline -10
docker events --since 24h
kubectl get events --sort-by=.lastTimestamp
journalctl --since "1 hour ago" | grep -i error
```

---

## Nguyen ly 3: Reproduce truoc, fix sau

```
❌ Doc log → doan nguyen nhan → fix ngay → deploy → "hy vong" het loi
✅ Doc log → doan nguyen nhan → reproduce loi → xac nhan nguyen nhan → fix → verify fix → deploy
```

Neu ban khong reproduce duoc, ban chua hieu van de.

---

## Nguyen ly 4: Don gian hoa — loai bo bien so

```
Loi: API khong response

❌ Debug from top: User → CDN → LB → Nginx → API → DB
   (qua nhieu bien so)

✅ Test tu trong ra ngoai:
   1. DB hoat dong khong?       docker exec db psql -c "SELECT 1"
   2. API goi DB duoc khong?    docker exec api curl localhost:5001/health
   3. Nginx forward duoc khong? curl -v http://nginx/api/health
   4. LB forward duoc khong?    curl -v http://lb/api/health
   → Tim diem dau tien bi fail
```

---

## Nguyen ly 5: So lieu, khong phai cam giac

```
❌ "Server cham lam" → lam gi day?
✅ "p95 latency tang tu 200ms len 2s tu 3h truoc" → co the debug

❌ "Memory leak" → sao biet?
✅ "RSS tang tu 200MB len 800MB trong 6h, khong giam sau GC" → confirmed leak
```

```bash
# Thu thap so lieu ngay khi co van de
docker stats --no-stream
free -h
df -h
ss -s
curl -w "TTFB: %{time_starttransfer}s Total: %{time_total}s\n" -o /dev/null -s URL
```

---

## Nguyen ly 6: Correlated ≠ Caused

```
"Deploy luc 2h chieu, loi luc 3h chieu → deploy gay loi?"

Co the:
- Deploy gay loi (nhung sao mat 1h moi loi?)
- Traffic peak luc 3h lam boc lo bug da co san
- SSL cert het han dung luc 3h
- Dependency service cung gap van de
```

**Correlated in time ≠ causation.** Phai chung minh, khong phai gia dinh.

---

## Nguyen ly 7: 5 Whys — hoi "tai sao" 5 lan

```
Van de: API tra 500 error

1. Tai sao 500?       → Database query timeout
2. Tai sao timeout?   → Query chay 30 giay
3. Tai sao query cham? → Full table scan, khong co index
4. Tai sao khong co index? → Migration script bi skip
5. Tai sao bi skip?   → CI pipeline khong chay migration step
                         → Root cause: CI pipeline config sai

Fix dung: Sua CI pipeline (root cause)
Fix sai:  Tang timeout len 60s (fix trieu chung)
```

---

## Nguyen ly 8: Thoat khoi "tunnel vision"

Khi debug qua lau, ban se bi "tunnel vision" — chi nhin 1 huong.

**Lam gi:**
- Nghi 10 phut, di uong nuoc
- Giai thich van de cho nguoi khac (rubber duck debugging)
- Hoi: "Neu khong phai o day, thi o dau?"
- Xem lai tu dau voi fresh eyes

---

## Debug checklist — Order of operations

```
1. [ ] Thu thap trieu chung (log, metrics, error message)
2. [ ] Tai tao duoc khong? (reproduce)
3. [ ] Co gi thay doi gan day? (deploy, config, traffic)
4. [ ] Thu hep pham vi (component nao?)
5. [ ] Dat gia thuyet
6. [ ] Kiem chung gia thuyet (1 thay doi / 1 lan)
7. [ ] Fix + Verify
8. [ ] Document (de nguoi khac khong phai debug lai)
```
