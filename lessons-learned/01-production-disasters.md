# Production Disasters — Bai hoc xuong mau

> "Moi su co production la mot bai hoc — neu ban khong hoc, ban se tra gia lan nua."

---

## 1. Deploy Friday chieu — "Chi la fix nho"

### Chuyen gi xay ra
Deploy 1 "hotfix nho" luc 5h chieu thu 6. Khong ai o lai kiem tra. Sang thu 2 phat hien API loi tu toi thu 6, mat 2 ngay du lieu.

### Nguyen nhan goc
- Khong co automated test
- Khong co health check sau deploy
- Khong co alert
- Tu tin qua muc: "co 2 dong code thoi"

### Bai hoc
- **KHONG BAO GIO deploy thu 6 chieu** (tru khi la hotfix critical va co nguoi theo doi)
- Moi deploy du nho deu can: test → deploy → verify → monitor
- 2 dong code co the lam sap he thong — complexity khong do bang so dong
- Dat rule: deploy window chi tu thu 2 den thu 4, truoc 3h chieu

---

## 2. Database migration khong co backup

### Chuyen gi xay ra
Chay `ALTER TABLE` xoa 1 cot tren production. Hoa ra cot do van dang duoc dung boi 1 service cu. Data mat vinh vien.

### Nguyen nhan goc
- Khong backup truoc khi migrate
- Khong kiem tra tat ca services nao dang dung cot do
- Khong co expand-contract pattern
- Khong test tren staging truoc

### Bai hoc
- **LUON backup truoc khi modify schema production**
- Dung expand-contract: them moi → migrate → xoa cu (xem `database-ops/05-migration-strategies.md`)
- Grep toan bo codebase tim ten cot truoc khi xoa
- Co kha nang rollback trong 5 phut

---

## 3. Disk full — silent killer

### Chuyen gi xay ra
Server chay binh thuong nhieu thang. Mot ngay app bat dau loi 500 random. Debug mat 4 gio moi phat hien: disk full 100%.

### Nguyen nhan goc
- Log files khong rotate → tang 200MB/ngay
- Docker images cu khong xoa → chiem 50GB
- Khong co monitoring disk
- Khong co alert disk > 80%

### Bai hoc
```bash
# Checklist phong tranh:
# 1. Log rotation (bat buoc)
logging:
  driver: json-file
  options:
    max-size: "10m"
    max-file: "3"

# 2. Docker cleanup (cron hang tuan)
0 3 * * 0 docker system prune -af --filter "until=168h"

# 3. Alert disk
# Prometheus: node_filesystem_avail_bytes / node_filesystem_size_bytes < 0.15
```
- Disk full gay nhieu trieu chung gia: database loi, app crash, container restart, log mat
- **Thu pham #1 cua loi "khong ro nguyen nhan"** la disk full

---

## 4. "Chay duoc tren may toi" — va hoa ra chi chay tren may ban

### Chuyen gi xay ra
App chay ngon tren local. Deploy len staging → loi. Nguyen nhan: local dung SQL Server 2019, staging dung 2016. 1 function khong ton tai.

### Nguyen nhan goc
- Moi truong dev khac production
- Khong co Docker cho dev
- Khong co CI/CD test tren moi truong giong production

### Bai hoc
- **Docker hoa moi thu** — dev, test, staging, prod deu chay cung image
- Pin version cu the: `postgres:16.2`, KHONG dung `postgres:latest`
- CI pipeline phai test tren image giong production
- "Works on my machine" la dau hieu cua infrastructure debt

---

## 5. Connection string trong source code

### Chuyen gi xay ra
Dev commit `appsettings.json` co connection string production. Repo la private, nhung 1 ngay CEO muon open source...

### Nguyen nhan goc
- Khong co `.gitignore` tu dau
- Khong dung secrets management
- "Repo private ma, lo gi"

### Bai hoc
- **Git khong bao gio quen** — du xoa file, history van con
- `.gitignore` phai la file DAU TIEN trong repo
- Dung environment variables hoac vault cho secrets
- Neu da commit secret: rotate ngay, coi nhu da bi leak

---

## 6. Auto-scaling gap memory leak

### Chuyen gi xay ra
App co memory leak nho (100MB/gio). HPA scale them pods. Moi pod moi cung leak. Cuoi cung cluster het resource, tat ca pods bi evicted.

### Nguyen nhan goc
- Memory leak khong duoc phat hien (tang cham)
- Auto-scaling che giau van de (them pod thay vi fix root cause)
- Khong co memory limit tren pod
- Khong co soak test

### Bai hoc
- Auto-scaling khong phai la solution cho moi problem
- **Memory limits bat buoc** — de leak boc lo nhanh (OOMKill > silent degradation)
- Soak test (chay load test 2-4h) de phat hien leak
- Monitor `GC Heap Size` trend — nen stable, khong nen tang lien tuc

---

## 7. Rollback khong duoc vi khong ai test rollback

### Chuyen gi xay ra
Deploy version moi bi loi. Rollback ve version cu → cung loi vi database da bi migrate (new schema, old code khong chay duoc).

### Nguyen nhan goc
- Database migration khong backward compatible
- Chua bao gio thu rollback
- "Rollback la chuc nang co san cua K8s" — dung, nhung chi rollback code, khong rollback database

### Bai hoc
- **Test rollback thuong xuyen** — rollback procedure phai duoc practice
- Database migration phai backward compatible it nhat 1 version
- Moi sprint, thu rollback 1 lan tren staging
- Tach deployment code va database migration
