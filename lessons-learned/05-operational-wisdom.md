# Operational Wisdom — Nguyen tac van hanh da duoc chung minh

> "Ops khong sexy, nhung ops giu cho he thong song."

---

## Nguyen tac 1: "Boring technology" thang

**Chon cong nghe nhàm chan, da duoc chung minh:**

| Boring (chon cay) | Shiny (rui ro) |
|---|---|
| PostgreSQL | NewDB 2024 |
| Redis | CustomCache |
| Nginx | TrendyProxy |
| Linux (Ubuntu LTS) | Cutting-edge distro |
| docker compose | K8s (cho 2 containers) |

Moi cong nghe moi = 6 thang hoc + bugs chua ai gap + community nho + docs thieu.

**Innovation tokens:** Ban chi co 3. Chi dung cho nhung gi THUC SU tao gia tri khac biet.

---

## Nguyen tac 2: Automate lan thu 3

```
Lan 1: Lam thu cong → ghi lai steps
Lan 2: Lam thu cong theo doc → cai tien doc
Lan 3: Viet script tu dong hoa
```

**KHONG automate ngay lan dau:**
- Chua hieu ro van de
- Requirements co the thay doi
- Ton thoi gian viet script cho viec chi lam 1 lan

---

## Nguyen tac 3: Moi thay doi phai revertable

```
Moi hanh dong tren production phai tra loi duoc:
"Neu sai thi revert the nao, mat bao lau?"

✅ Deploy new version → rollback = deploy version cu (2 phut)
✅ Feature flag on → revert = flag off (30 giay)
❌ DROP TABLE → khong revert duoc (tru khi co backup)
❌ ALTER TABLE DROP COLUMN → data mat vinh vien
```

---

## Nguyen tac 4: "If it's not monitored, it doesn't exist"

```
Service chay khong co metrics     → ban KHONG BIET no dang chay tot
Service khong co alert            → ban CHI BIET khi user bao
Service khong co log              → ban KHONG THE debug
Service khong co health check     → orchestrator KHONG THE tu heal
```

**Minimum viable monitoring:**
1. Health check endpoint
2. Error rate alert
3. Response time alert
4. Disk/CPU/RAM alert
5. Log co structured format

---

## Nguyen tac 5: Document khi no con nong

```
Vua fix xong 1 incident luc 3h sang?
→ Viet postmortem NGAY, khong doi den sang thu 2
→ Khi con nho ro chi tiet, timeline, cam xuc

Deploy 1 cai kho?
→ Ghi lai steps NGAY sau khi xong
→ Doi 1 tuan la quen het
```

---

## Nguyen tac 6: Graceful degradation > Total failure

```
❌ Database cham → API tra 500 cho tat ca requests
✅ Database cham → API tra cached data + header "X-Data-Stale: true"

❌ Payment service down → User khong dat hang duoc
✅ Payment service down → User dat hang, payment retry later

❌ Search service down → Trang home die
✅ Search service down → Trang home hien "Search tam thoi khong kha dung"
```

**Design for failure:** Moi dependency phai co fallback plan.

---

## Nguyen tac 7: Runbooks > Tribal knowledge

```
❌ "Hoi anh A, chi anh A biet cach restart service nay"
   → Anh A nghi phep → khong ai biet

✅ Runbook:
   ## Restart Payment Service
   1. SSH vao server: ssh devops@10.1.1.100
   2. docker compose restart payment
   3. Verify: curl http://localhost:5002/health
   4. Kiem tra log: docker logs --since 5m payment
   5. Neu van loi: escalate den team lead
```

---

## Nguyen tac 8: Least privilege — cho it nhat co the

```
❌ Moi nguoi co root access
❌ App chay bang root user
❌ Database user co quyen DROP

✅ Dev chi co read access production
✅ App chay non-root, chi co quyen can thiet
✅ App database user: SELECT, INSERT, UPDATE (KHONG co DROP, ALTER)
✅ CI/CD service account: chi co quyen deploy
```

---

## Nguyen tac 9: Pre-mortem > Post-mortem

```
Post-mortem: "Chuyen gi da xay ra?" (sau su co)
Pre-mortem:  "Chuyen gi CO THE xay ra?" (truoc su co)

Moi sprint, hoi team:
"Neu he thong sap tuan nay, nguyen nhan la gi?"
→ Database het connection? → Tang pool, add monitoring
→ Disk full? → Add cleanup cron
→ SSL cert het han? → Add cert monitoring
```

---

## Nguyen tac 10: Ship small, ship often

```
❌ Gom 50 features deploy 1 lan / thang
   → Loi → khong biet feature nao gay loi
   → Rollback → mat 50 features
   → Risk cao → team so deploy

✅ Deploy 1-2 features / lan, 3-5 lan / tuan
   → Loi → biet ngay feature nao
   → Rollback → chi mat 1 feature
   → Risk thap → team tu tin deploy
```
