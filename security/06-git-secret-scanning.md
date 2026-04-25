# Git Secret Scanning & Pre-commit Hooks

> Ngay bat dau: 2026-04-25

## Tai sao can Secret Scanning?

- Dev hay co thoi quen hardcode mat khau hoac token vao code roi quen xoa truoc khi commit.
- Mot khi da commit va push, secret do nam mai mai trong Git history. Xoa o commit tiep theo la VO NGHIA.
- Giai phap: Kiem tra (scan) MỌI file TRUOC khi cho phep commit. Thu thuat nay goi la **"Shift-left Security"**.

---

## 1. Cai dat Gitleaks

[Gitleaks](https://github.com/gitleaks/gitleaks) la cong cu tieu chuan de scan secrets.

```bash
# Cai dat tren macOS
brew install gitleaks

# Quet toan bo thu muc hien tai (khong can phai la git repo)
gitleaks detect --no-git -v

# Quet tuong lai (cac file sap duoc commit - staged files)
gitleaks protect -v --staged
```

---

## 2. Thiet lap Pre-commit Hook

Git ho tro `hooks` - nhung script tu dong chay o cac su kien nhat dinh. Ta se dung `pre-commit` hook de chay `gitleaks`.

### Buoc 2.1: Tao file hook
Tao file `.git/hooks/pre-commit` voi noi dung:

```bash
#!/bin/sh

# Kiem tra cac file dang nam trong staging area
gitleaks protect -v --staged

# Neu gitleaks phat hien secret (exit code khac 0), huy commit
if [ $? -ne 0 ]; then
  echo "\033[31m[ERROR] Gitleaks detected possible secrets! Commit aborted.\033[0m"
  exit 1
fi
```

### Buoc 2.2: Cap quyen thuc thi
Khong co quyen nay thi hook khong chay duoc.

```bash
chmod +x .git/hooks/pre-commit
```

Tu gio tro di, neu gõ `git commit` ma co chuoi nao nghi la password, Git se bao loi va dung ngay lap tuc.

---

## 3. Quy tac lam viec de khong bi tool chan "oan"

Gitleaks nhan dien secret dua tren **Ten bien (Keyword)** va **Do phuc tap cua gia tri (Entropy)**.

### A. Khi viet code that (Production/Dev)

- KHONG de secret trong code. Chuyen sang file `.env` (va file `.env` PHAI nam trong `.gitignore`).
- Su dung Azure Key Vault, AWS Secrets Manager, hoac Hashicorp Vault (xem bai `03-secrets-management.md`).

### B. Khi viet file mau / documentation

Cac file `.md` hoac `.sample` can phai duoc push len Git de chia se kien thuc. Lam sao de dua vi du ma khong bi Gitleaks chan?

**Quy tac 1: Placeholder ro rang, Entropy thap**
Tool se KHONG chan neu gia tri ro rang la do con nguoi tao ra va khong phuc tap (vi du: toan viet HOA).
- ❌ `DB_PASS=password123` (De bi chan vi giong mat khau thuc su - neu co base64)
- ❌ `API_KEY=dGhpcyBpcyBhIHZlcn...` (De bi chan do co entropy cao)
- ✅ `DB_PASS=YOUR_PASSWORD_HERE` (Tot)
- ✅ `API_KEY="<INSERT_TOKEN_HERE>"` (Tot)

**Quy tac 2: Dat ten bien chuan (Key Naming Convention)**
Luon dat ten bien ro nghia de sau nay khi implement Secret Scanner, tool se tu dong target. 
Cac tu khoa nen co trong ten bien: `password`, `secret`, `token`, `api_key`, `credential`, `auth`.
- ✅ `REDIS_PASSWORD`
- ❌ `REDIS_CONN_STR_PART2` (Gitleaks se co the bo qua khong quet ki gia tri)

### C. Khi sinh mat khau that
De dam bao khi secret lọt ra ngoai se BỊ GITLEAKS BAT DUOC 100%, hay ap dung **Secret Prefixing**.
- ❌ Mat khau sinh ngau nhien: `9xK2mPq$L8` (Tool co the bo xot vi nhin giong 1 doan string random binh thuong)
- ✅ Mat khau co tien to nhan dien: `mycorp_prod_9xK2mPq$L8`
- Khi do ban co the configure gitleaks block tuyet doi moi text nao co chu `mycorp_prod_`.

---

## 4. Hieu ro han che: Secret Scanner KHONG PHAI la Password Checker

Day la mot bai hoc xuong mau ve ky thuat (technical nuance) cua cac he thong quet:

**Cong cu nhu Gitleaks duoc thiet ke de tim Token cua he thong, chu khong phai mat khau do con nguoi tao ra.**

### Bieu thuc chinh quy (Regex) vs Ky tu dac biet
Da so cac token that cua cac hang (AWS, Stripe, Github, JWT...) deu duoc ma hoa duoi dang `Base64` hoac `Hex`. Bảng mã này **chỉ bao gồm** chu cai (`a-z`, `A-Z`), so (`0-9`) va dau gach ngang (`-`, `_`). Chung **KHONG BAO GIO** chua cac ky tu dac biet nhu `@`, `$`, `#`.

Vi vay, luat quet cua Gitleaks (VD: rule `generic-api-key`) thuong chi gioi han ở bieu thuc Regex: `[a-zA-Z0-9\-_]{16,64}`.

### Chuyen gi xay ra neu Dev go mat khau cuc kho?
Neu ban hardcode: `DB_PASSWORD: abc@123hvnhiycpoui0876$567v2jdl#@vn` (do dai tren 30 ky tu, do phuc tap cuc cao).

**Ket qua: Gitleaks SE BO QUA!**
Li do: Khi no doc thay cac ky tu `@`, `$`, `#`, engine Regex cua Gitleaks bi "lech pha" khoi quy luat cua mot Token tieu chuan. No tu dong hieu rang *"Day la mat khau do con nguoi tu go"* chu khong phai Token cua he thong, va no khong chan. (Dau vay, Gitleaks van se catch neu ban viet dung luat rieng cho no, nhung luat mac dinh thi khong).

### Nguyen tac phong thu nhieu lop (Defense in Depth)
Vi ly do tren, nguyen tac toi thuong trong DevSecOps la **Tool quet chi phat hien duoc 1 phan, KHONG BAO GIO la 100%**. 
Gitleaks la chot chan (guardrail) vao phut chot, chu khong the thay the cho:
1. Tu duy su dung `.gitignore` dung cach.
2. Phan quyen truy cap va su dung he thong Secrets Vault (Azure Key Vault, Hashicorp Vault).
